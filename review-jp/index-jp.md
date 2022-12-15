---
layout: simple
title: Review
categories: []
tags: []
status: publish
published: true
meta: {}
permalink: /review-jp/
---

# Googleのコードレビューガイド

この文書は、Googleの[Engineering Practices Documentation](https://google.github.io/eng-practices/)の一部である[Code Review Developer Guide](https://google.github.io/eng-practices/review/)のまとめを訳したものです。原文でボールド体とbイタリック体で強調した内容はほとんど盛り込まれており、Googleの経験が込められた内容も抜け落ちなく盛り込もうとしました。

## 用語の定義
- `CL`：「changelist」の略語。ソースコード管理（version control）に反映されるか、またはレビューを受けているコードの集合単位。
- `LGTM`：「Looks Good To Me」の略語。レビューを通過させる時にレビュアーが残す言葉だ。

訳注：その他よく登場する単語は、以下のように統一して使用しました。
- `CL作者`: 作成者
- `Reviewer`: レビュアー
- `Code health`: コード品質

## 紹介

この文書は、Googleの開発者がコードレビューを行う過程と守るべき原則が含まれている。大きく２つに分けることができる。

- [コードをレビューする方法]({{ site.baseurl }}{% link review-jp/reviewer.md %})：コードレビュアー向けのガイド
- [CL作成者向けのガイド]({{ site.baseurl }}{% link review-jp/author.md %})：レビューを受けるコード作成者のためのガイド

## 適切なレビュアーの選定

あなたのコードをきちんとレビューしてくれる人が適したレビュアーだ。通常、元のコードの作成者が有力だ。時にはCLの各部分を他のレビュアーに任せることもある。適切なレビュアーを見つけたがレビューをする状況ではない場合は、少なくとも参照（CC）に含める。

## 対面レビュー

良いコードレビューをする資格のある人とペアプログラミングをした場合、そのコードはレビューを受けたと見なす。対面レビューを進めることもできるが、この場合にはレビュアーが質問をし、作成者は質問を受けた時に限って発言をすることができる。
