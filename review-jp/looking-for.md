---
layout: simple
title: What to look for in a code review
categories: []
tags: []
status: publish
published: true
---

# コードレビューでのポイント

## デザイン

コードレビューをする際に最も重要なのは、全体的なコードの設計だ。各部分がうまく設計されているか？そのコードベースに追加すべきか、それとも別のライブラリに追加すべきか？今の時点で機能を追加するのは正しいのか？

## 機能

作成者が意図したとおりにコードが動作しているか？コードの動作はユーザーにとって有益か？「ユーザー」はプログラムの消費者であったり、そのコードを使用する他の開発者でもある。レビュアーはエッジケースも考慮しなければならない。特にUIの変更がある場合は、機能がうまく動作していることを確認する。

また、CLに並行作業（concurrency）がある場合は、デッドロックや競合状態が発生しないかを慎重に検討する必要がある。この種の問題は、コードを実行しても見つけるのが容易ではないので、十分に検討しなければならず、もし危険が発見された場合、並行作業を除去するほうが良い。

## 複雑度

コードをもっと簡単にすることはできないのか？行別、関数別、クラス別に全て見て確認しよう。複雑なコードは理解に時間がかかり、使用するサイやメンテナンスする際にバグが発生する可能性が高い。オーバーエンジニアリングも不要に複雑さを高めます。レビュアーは、オーバーエンジニアリングが起こらないように特別に注意し、将来の問題は実際に発生した時に解消するようにする。

## テスト

単体テスト、統合テスト、E2Eテストを作成する。[緊急事態]({{ site.baseurl }}{% link review-jp/emergencies.md %})ではなく一般的な状況では、本番コードとテストコードは同じCLに入る必要がある。通常、テストコードに対するテストコードは作成しないため、テストが適切かは必ず人が確認する必要がある。コードが壊れればテストも失敗するか、適切な単位に分かれているかなどを確認してみる。テストコードもメンテナンスが必要なコードなので、これも不要に複雑ではないかを確認する。

## 名前

変数、クラス、メソッド名が明瞭に作成されているか？良い名前の対象は何か、何をするのかを十分に説明しながらも、読みにくいほど長くない。

## コメント

コメントは明瞭で役立つか？本当に必要なコメントだけがあるか？有用なコメントとは、コードが何をするのではなく、なぜ存在するのかを含めている。コードだけを見て何をするのか分からない場合は、コードをより簡単に修正する必要がある。正規表現や複雑なアルゴリズムについては例外で何をするかを書いても良いが、一般的にはコードに収められない情報をコメントに残したほうが良い。

## スタイル

Googleで使われるメジャー言語は全てスタイルガイドがある。スタイルガイドにない事案は個人の好みに任せるため、これでコードレビューを遅らせてはならない。そして、コードスタイルの変更は別途CLとしてレビューされるべきです。スタイル変更とコード修正を一度にすると、マージとロールバックが難しくなり、レビューをするのも難しい。

## 文書

コードに対応する文書を正しく最新化させたか？CLの変更がビルド、テスト、配布方式を変えると、必ず文書も一緒に更新する。

## 全ての行

レビュアーとしてCLの全ての行をレビューする必要がある。自動生成コードや規模の大きいデータ構造なら目を通すだけで良いが、人が作成したコードは必ず注意深く検討する。 
もしコードが見にくいのでレビューが遅れば、作成者に説明を求める。Googleは優秀な開発者を採用するため、あなたが理解できないコードは他の開発者も理解できない。あなたが正しくレビューできない特別な部分（セキュリティ、パラレル、アクセシビリティ、i18nなど）があれば、資格のあるレビュアーを指定する。

## コンテキスト

修正された一部分だけを見ないで、全体的なコンテキストを把握する。修正を含む関数やクラス全体を確認する。修正はわずか数行であっても、後で見るとそれが100行の関数に含まれている可能性もある。**コード全体の品質を低下させるCLは絶対に承認しない。** 通常、小さな変化が集まって全体の複雑度を増やすため、新しく追加されたコードにはわずかな複雑度も許可しない。

## 良い点

CLをレビューして良い点を見つけたら、作成者に言及する。コードレビューをしていると間違いだけを指摘することがあるが、良い部分には賞賛と感謝を惜しまない。メンタリングの観点から、間違いよりはよくできたことを教えてくれる方が価値がある。

## まとめ

コードレビューをしながら、次の項目がよく守られていることを確認する。

- コードはうまく設計されているのか
- ユーザーに有用な機能を提供するのか
- UIの変更は合理的で、見た目は良いか
- 並列作業は安全に実行されるのか
- 余計に複雑になっていないのか
- 今すぐ必要ない機能を開発してはいないか
- 単体テストが適切なのか
- テストはうまく設計されているのか
- 全ての名前がうまくつけられているのか
- コメントは明瞭で有用なのか、「何」よりは「なぜ」を表しているのか
- ドキュメントに反映されているのか
- スタイルガイドに従っているのか

レビュアーは全ての行をレビューし、全体的なコンテキストを見て、 コード品質を改善させ、作成者がよくできた点は必ず賞賛する。

次：[レビュー中のCLを詳しく見る]({{ site.baseurl }}{% link review-jp/navigating-cl.md %})