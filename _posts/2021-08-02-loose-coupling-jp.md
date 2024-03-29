---
layout: post
title: "モバイルアプリの疎結合"
categories: []
tags:
- loose coupling
- interface
- dependency injection
type: post
published: true
meta: {}
draft: true
---

プログラミングは正常に動作するソフトウェアを効率的に作り出すことであり、
それをうまくするためには、メンテナンスが簡単なコードを作成する必要がある。そして、メンテナンスが簡単なコードを作成する非常に効果的な方法は、オブジェクトを疎結合（loose coupling）にすることだ。Gang of Fourはデザインパターンで次のように述べた。

```
Program to an interface, not an implementation.
```

実装に依存せず、インターフェースに依存する結合関係を疎結合という。疎結合されたコードは、スケーラブルでメンテナンスがはるかに簡単だ。プログラムが大きくなり複雑になっても管理できる。

疎結合されたコードを作成することができるようにするソフトウェア設計の原則やパターンを依存性注入と呼ぶ。 密結合された（tightly coupled）コードにはない要素なので、このような追加の要素がなぜ必要なのかを納得するには、疎結合されたコードの利点をまず理解する必要がある。

### 1. 遅延バインディング（Late Binding）が可能

コードを再コンパイルしなくても実装体を入れ替えることができる。ぴったり当てはまる例ではないが、モバイルアプリの観点から見ると、OSがアップデートされたり、Apple/Androidが提供するフレームワークがアップデートされても、あえてアプリを新しくデプロイしなくても、ユーザーは新機能を体験できる原理と似ている。

しかし、これはモバイルアプリ開発者としては実感できない。なぜなら、アプリは開発者が定義して実装した環境でのみ実行されるため、事実上実装体を入れ替えることはないからである。例えば、データベースが必要なアプリを作成する時に、アプリ開発者自身が導入したデータベース以外の他のデータベースを使用する環境で自分のアプリが実行されるという仮定や考慮はする必要がない。従って遅延バインディングは、モバイル開発者が享受できる利点であるとは言えない。しかし、疎結合の利点はこれが全てではない。

### 2. 拡張と再利用が簡単

アプリはユーザーに合わせて要求仕様が変わり、事業に合わせて進化し続ける。クラス/モジュールを疎結合にすることで、新機能を開発したり、既存の機能を修正・拡張したりするのが簡単になる。疎結合されたコードは拡張には開いており、変更には閉じている。疎結合されたコードでは、オブジェクトを組み立てるポイント（composition root）が別にあり、ここで実際の実装体を生成して注入するため、インターフェースを使用している側のコードは変更する必要がない。

例えば、アプリに決済機能がある場合、商品側のコードが決済関に関するインターフェースに依存していれば、決済の流れを改善・改編する時に決済機能をインポートする商品側のコードは修正する必要がない。更に一般化してA/Bテストも検討できる。同じ機能を様々な方法で提供する必要がある場合は、その機能を呼び出す側のコードを変更せずに新機能を追加・削除することができる。

### 3. 並列開発が可能

プロジェクトとチームが大きくなると、１つのコードベースで複数の開発者が並列的に働くことになる。コードが疎結合になっていると、開発者は複数のモジュールを同時に開発するのが簡単だ。

Grabのモバイル開発チームは、100人以上のiOS開発者がモビリティ、フード、フェイメント、CXなど10個未満の大きなTF（Tech Family）に分かれている。各チームは、他のチームが開発する機能（リアルタイムチャット、決済、残高照会、リワードポイント、ユーザー情報/認証など）を活用しながらも、疎結合されたコードのおかげで「スーパーアプリ」を同時に開発することができる。

### 4.メンテナンスが簡単

クラスとモジュールの境界と役割が明確に区別されているコードはメンテナンスしやすい。 
新機能を開発する際に、どこを修正すればよいのか早く把握でき、影響範囲を簡単に見ることができる。一つの役割だけをするクラスやモジュールに結合されたコードではデバッグもあまり難くない。役割と範囲がよく分離されていると、バグを引き起こした可能性のある地点を簡単に絞ることができるからだ。

### 5.テストが簡単

正確に言えば、単体テストが簡単になる。単体テストを行うには、テスト対象を依存関係から隔離させる必要がある。オブジェクト同士が疎結合になっていると、テスト帯域への置き換えが簡単だ。

単体テストの必要性やコード構造のテスタビリティを評価しない人もいるだろう。
Jetbrainの2021年開発者アンケートを見ると、62%のSwift/Obj-C開発者は単体テストを作成しないという。しかし、自動化されたテストの利点を一度でも経験してみると（少し誇張して）、テストコードを作成していなかった時代には戻れない。

単体テストはかなりのテスト作業を自動化できるようにする。Grabでは100人以上の開発者が月に数十万行のコードを作成しているが、単体テストなしで全ての新しいコードと既存コードの動作を手動で検査することは事実上不可能だ。Grabで私たちのチームが新しく作られた当初は、コードカバレッジが低かったが、１年以上テストを追加した結果、実際に回帰バグと重大バグの数が著しく減少した。

## まとめ

ロバート・マーティンによると、オブジェクト指向言語が開発者に与えてくれた最も強力な武器は多型を安全に使うことができるようになったことだという。多型が安全になったことで、開発者は自分のシステムが依存している全ての実装体（implementation detail）を交換可能なプラグインにすることができる。システム同士、モジュール同士を疎結合にすることができるようになった。ロバート・マーティンにはさらに、こんな方法があることを知りながら、システムをこのように設計しない理由が一体何なのかと反論する。([The Future of Programming Languagesの講演中](https://youtu.be/ya1xDCCMh7g?t=4728))
