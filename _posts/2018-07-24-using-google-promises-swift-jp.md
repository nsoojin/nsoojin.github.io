---
layout: post
title: "google/promisesを活用したSwift非同期プログラミングとエラー処理"
categories: []
tags:
- swift
- promises
- async programming
- error handling
type: post
published: true
meta: {}
draft: true
---

## Background

今年1～2月頃、偶然にgoogle/promisesを知ることになったが、紹介文とサンプルコードの数行を見てから「これはぜひ使いたい」と思った。何をするフレームワークなのかは、GitHubによく紹介されているので詳細な説明は省略するが、一言でいえば、非同期作業に対する結果をcompletion handlerで処理するiOSの特性に起因する nested closures問題を解消することができる。更に、エラー処理まですっきりとなる。

Promisesを見てすぐに使いたいと感じた理由は、私が以前からコードで試そうとしたことがあったが、このライブラリを使えばそれが実現できると思ったからだ。

- サイドエフェクトのない関数のチェイニング
- ユーザーにとって意味のあるエラー処理
- 最小限のチューニング（ディペンダンシー）

これらの小さな目標は、作業の流れの読みやすいコードを作成し、ユーザーにより良い経験を与えたいという最終的な目標に役立つと考えた。

### Chaining functions w/o side effects

2016年にD2 iOSオープンセミナーで発表したことをきっかけに、Naver社のPapagoとWhaleブラウザを開発したチームのリーダーを知り、そのチームで短期プロジェクトを行ったことがある。メンターから学んだ色々な内容の中で最も記憶に残り、今もコーディングしながら毎日実践するために努力するのは「関数は10行を超えないように」である。刺激的に聞こえるかもしれないが、本質的に関数は１つの作業だけをするように作成するというルールとして理解した。10行以内に作成する努力をしてみると、引き続き関数は小さな機能単位に分割され、オブジェクト間の関係を設計するのに悩む時間が増える。さらに、サイドエフェクトのない関数をチェイニングすると、コードが読みやすくなり、修正が容易になるという利点がある。特にSwiftのmap、flatMap、compactMap、filterなどのメソッドと一緒に使う時に効果を発揮する。Promisesでは、then、always、validateなどで関数を繋げることができる。

例：
```swift
extension CNContact {
  var mainPhoneNumber: String? {
    return phoneNumbers.map { $0.value.stringValue }
                        .filter(prefixValidater)
                        .map(replaceKoreanCountryCode)
                        .map(removeNonNumerics)
                        .filter { $0.isPhoneNumber && $0.count == 11}
                        .first
  }

  private func prefixValidater(_ target: String) -> Bool { ... }
  private func replaceKoreanCountryCode(_ digits: String) -> String { ... }
  private func removeNonNumerics(_ digits: String) -> String { ... }
}
```

### Conveying meaningful error messages to users

モバイルアプリの開発を数年してみると、どうすればエラー処理がうまくできるかをもっと知りなくなった。モバイルアプリで最も起こっているタスクの段階は、ユーザーインタラクション 👉 リクエストを処理するための一連のタスク 👉 画面に結果を表示する、である。しかし、一連のタスクを処理する過程で色々なエラーが発生する可能性がある。データが変更されたり、ネットワークが不安定になったり、サーバーがダウンしたり、権限がなかったりなど。この時、単に「リクエストが失敗しました」という意味のないメッセージではなく、エラーの原因をユーザーに知らせるのがユーザーエクスペリエンスの面で格段に良い。NSErrorのlocalizedDescriptionを活用したり、Swiftでは ErrorまたはLocalizedErrorプロトコルを使用することができる。Promisesでは、複数の連続した非同期作業中に発生したエラーを最終段階で統一的に伝達され、さらにrecoverで回復することもできる。

このようなエラーメッセージを表示するのか？

<img src="/assets/posts/unhelpful-error-message-jp.png" width="300" />

それとも、実際にユーザーに役立つメッセージを表示するのか？

<img src="/assets/posts/meaningful-error-message-jp.png" width="300" />

### Minimizing dependencies

スタンダードライブラリを包んだ〜Kitを使うことへの微妙な拒否感がある。 私は、iPhoneもケースなしで保護フィルムだけをつける性格なので、１～２のプロジェクトだけで使うために、あるいはサイズが１の機能を使うためにサイズが10のライブラリをココアポットに追加するのはなるべく避けたい。オートレイアウトも別途ライブラリなしで使っており、かつて興味を持っていたRxも勉強していたが、結局過度なチューニングをしているようで都外視した。チューニングの終わりは純正だとも言われるが、純正を好む私にとって、Promisesは他の類似ライブラリより[GCD](https://developer.apple.com/documentation/dispatch)をより軽く包み込んだので性能面で優れており、学習コストも少ないようだ。

## Adopting Promises to Your Project

Promisesを導入するからといって、すぐにプロジェクト全体を取り除く必要がないので、実務でじっくり適用してみるのにも良い。通常、タスクの特性別に担当クラスがあるが（e.g。ログインマネージャ、イメージダウンローダクラスなど）このような１～２のクラスに優先的に適用しながら試して見ても良い。また、[`wrap`](https://github.com/google/promises/blob/master/g3doc/index.md#wrap)を使うと、既存のコードを修正する必要もなく、数行コードだけでPromises式の非同期関数を作成することもできる。

既存のcompletion handlerをパラメータとして受け取った関数： 
```swift
func data(from url: URL, completion: @escaping (Data?, Error?) -> Void)
```

Promiseオブジェクトを返すように変更した非同期関数：
```swift
func data(from url: URL) -> Promise<Data>
```

その後、関数内で[Promise](https://www.github.com/google/promises/blob/master/g3doc/index.md#creating-promises)オブジェクトを生成して返し、[fulfill](https://github.com/google/promises/blob/master/g3doc/index.md#then)された/される結果値を用いて追加の作業をすれば良い。

```swift
let url = ...
data(from: url).then { data in
  //dataによる追加作業
}.catch { error in
  //error処理
}
```

より多彩な活用法は文書に簡単に説明されている！

## Use Cases

これまで使ってみながら試行錯誤を経て習得したいくつかのユーズケース及び注意事項をまとめた。

### Partial Application手法

Promisesパイプラインの読みやすさと関数の再利用性を高めるためのpartial application手法を活用する方法を紹介する。map、forEachなどのhigher order functionを使っていたら、恐らく使ったことがある可能性が高い。

APIサーバーにログインしてアクセストークンを受け取るタスクは、ログインベースのサービスでは欠かせない作業だ。仮想ログイン段階は次の通りである。会員登録 👉 ログイン 👉 アクセストークン獲得。しかし、すでに会員登録になっているユーザーであれば、会員登録に失敗することになる。Promisesでは、失敗した時にrecoverを使用して失敗を回復する機会があります。そのため、会員登録の失敗の原因がduplicate userなら、ログインを試みる。

Promisesコード：
```swift
typealias MyAccessToken = String

func retrieveAccessToken(with naverToken: String) -> Promise<MyAccessToken> {
  return requestSignUp(with: naverToken)
         .then(signIn(with: naverToken))
         .recover(onError(with: naverToken))
}

//Async Server API calls
func requestSignUp(with naverToken: String) -> Promise<SignUpResponse> { ... }
func requestSignIn(with naverToken: String) -> Promise<MyAccessToken> { ... }

//partially applied functions
func signIn(with naverToken: String) -> (SignUpResponse) -> Promise<MyAccessToken> {
  return { _ in requestSignIn(with: naverToken) }
}

func onError(with naverToken:String) -> (Error) -> Promise<MyAccessToken> {
  return { error in
    switch error {
    case SignUpError.duplicateUser:
      return requestSignIn(with: naverToken)
    default:
      return Promise(error)
    }
  }
}
```

signIn(with:)とonError(with:)は、それぞれSignUpResponseと Errorパラメータを後で渡されるように作成されたpartially applied functionsである。このようにチェイニングする時は、すぐにクロージャを使うことなく、partial applicationを活用して既存のAPI関連関数を再利用すると同時に、非同期作業パイプラインをはるかに読みやすくした。


### 単純なチェイニングでは不可能な作業は、awaitを活用する

awaitは、複数の非同期操作から得られた結果を組み合わせて使用する必要がある場合に便利だ。

例えば、サムネイル画像のurlを用いてUIImageとその画像の代表UIColorを抽出して画面に描画する作業があるとしよう。過程をまとめてみるとURLをUIImageに変換 👉 UIImageからUIColorを抽出する 👉 UIImage、UIColorを用いて画面にサムネイルを生成する、だったが、この作業はthenチェイニングだけでは実装するのが難しく、awaitで解決した。また、この方式は、非同期操作を同期的なコードのように使いたい時に使用しても良い。

Promises コード：
```swift
typealias ThumbnailData = (image: UIImage, color: UIColor)

func thumbnailData(from url: URL) -> Promise<ThumbnailData> {
  return Promise<ThumbnailData>(on: queue) { //queue는 백그라운드 DispatchQueue
    let image = try await(image(from: url))
    let color = try await(dominantColor(from: image))

    return (image: image, color: color)
  }
}

//Async functions
func image(from url: URL) -> Promise<UIImage> { ... }
func dominantColor(from image: UIImage) -> Promise<UIColor> { ... }

```

活用した部分：
```swift
let cell: MyTableViewCell = ...
let myDatum = data[indexPath.row]
let url: URL = myDatum.imageURL

thumbnailData(from: url).then { result in
  cell.imageView.image = result.image
  cell.dominantColorView.backgroundColor = result.color
}

```

更にPromisesを使用する前に知っておくと良いことには、
- [アンチパターンを避ける](https://github.com/google/promises/blob/master/g3doc/index.md#anti-patterns)
- [retain cycle](https://github.com/google/promises/blob/master/g3doc/index.md#ownership-and-retain-cycles)を念頭に置く
- いくつかの有用な[extensions](https://github.com/google/promises/blob/master/g3doc/index.md#extensions)

などがある。

## Wrap Up

iOS開発をしながら、一度でもcompletion handler方式の非同期プログラミングに物足りなさを感じてみたり、Rxは必要以上にあまりにも膨大だと思ったことがある場合は、一度試してみることをお勧めする。google/promisesは最小限のコード変更だけで非同期コードを柔軟にし、読みやすさを高め、少ない学習コストで色々試してみることができるスケーラビリティのあるフレームワークだと思う。
