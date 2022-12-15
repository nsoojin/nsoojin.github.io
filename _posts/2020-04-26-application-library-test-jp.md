---
layout: post
title: "XCTest所要時間を短縮する"
categories: []
tags:
- xctest
- application test
- library test
- ios testing
type: post
published: true
meta: {}
draft: true
thanks: 
- 류성두 님
---

最近まで、XCTestを単体テストと統合テストに区分していたが、単体テストも、ランタイムの動作方法によってapplication testとlibrary testに分けることができることが分かった。両者の違いと使い方をまとめた。

## Application Test vs Library Test

<img src="/assets/posts/application-library-test.png" />

単体テスト対象のHost Application項目でアプリexecutableを選択するとapplication testになり、Noneを選択するとlibrary testになる。Xcodeで単体テスト対象を作成する時、初期設定を変更しなかった場合は、デフォルトとしてHost Applicationが選択される。だから、これまではあまり考えずにほとんどのテストをapplication testとして作成していた。しかし、目的に応じて、これらの２つを区別して使用すると明らかな利点がある。

## Application Test

iOSアプリに関する部分（UIViewController、UIWindow、UIViewなど）を検査する単体テストは、application testバンドルに含める必要がある。Application testバンドルにはテストを実行するためのホストアプリが必要で、ホストアプリをインストールするiOSシミュレータも必要である。

Application testの欠点は次の通りだ。

アプリのライフサイクルが回るが、この時タイミング問題が発生し、テスト結果が変わることがある。
シミュレータは同時に１つのホストアプリしか実行できないため、application testの対象は１つのシミュレータでパラレルなテストが機能しない。

## Library Test

Library testは、アプリに関係のないロジックをテストするために使用される。 
Library testは、ホストアプリをシミュレータにインストールする必要がないため、テストが速く、たまに動作が不安定なシミュレータの影響を受けにくい。

一方、library testでは動作しないiOS APIが少しある。そのような場合は、application testで確認する必要がある。

単体テストでユーザーのボタンタブをまねるために使用するUIControlの func sendActions（for controlEvents: UIControl.Event）は動作しない。
任意のUIWindowをkeyWindowにすることはできない。ホストアプリもないのでUIWindow自体がない。そのため、UIViewをレンダリングしてから検査するスナップショットテストはできない。
Keychain関連API

（などもある）

## Library Testを使うと良い理由

Application testはホストアプリのプロセスに注入されるため、シミュレータにアプリがインストールされ、実行（app launch）までになってからテストコードが動作し始める。一方、library testはアプリに依存せず、アプリをインストールせずにiOSシミュレータ内のxctest
executable($DEVELOPER_DIR/Platforms/iPhoneSimulator.platform/Developer/Library/Xcode/Agents/xctest)やmacOSのxctestコマンドラインのプロセス(/usr/bin/xctest/)で実行される。したがって、application testをlibrary testに切り替えると、転換した対象の数*アプリのインストールと実行にかかる時間と同様の時間を短縮できる。また、ホストアプリを待つ必要がなく、並列で迅速にテストすることができる。

## Library Testに切り替える方法

まず、テスト対象のGeneralタブに行き、Host ApplicationをNoneに変更してテストを実行する。問題が発生した場合は、次のように解決する。

### 1. テストバンドルが実行されていない場合

以下のようなランタイムエラーを出す。

```
Library not loaded. 
(中略)
Reason: image not found
```

Application testはホストアプリに注入されるため、アプリ対象にリンクされているライブラリを使用できる。しかし、library testは独立したプロセスで実行されるため、テスト対象に外部依存関係を直接リンクする必要がある。ココアポットを使用している場合は、Podfileを開き、アプリが使用している外部依存関係をテストターゲットに追加する。

### 2. テストケースが失敗した場合

実行はされるが、テストケースが失敗した場合は、iOSアプリに依存関係のあるAPIを使用した可能性が高い。従って、対象を分離しlibrary testで失敗したテストはapplication testに移動させる。つまり、iOSアプリが必要なテストのみをapplication testで実行し、残りはlibrary testで実行すればテストの所要時間を短縮できる。
