---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
---

# Scala ActiveRecord [![maven central](https://maven-badges.herokuapp.com/maven-central/com.github.aselab/scala-activerecord_2.13/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.aselab/scala-activerecord_2.13) [![javadoc](http://javadoc-badge.appspot.com/com.github.aselab/scala-activerecord_2.13.svg?label=scaladoc)](http://javadoc-badge.appspot.com/com.github.aselab/scala-activerecord_2.13/com/github/aselab/activerecord/index.html?javadocio=true)

[`Scala ActiveRecord`](https://github.com/aselab/scala-activerecord){:target="_blank" rel="noopener"} はScala用ORMライブラリです。

このライブラリはRuby on RailsのActiveRecordの着想から生まれたものです。  
「設定より規約」、「DRY原則」の設計思想でアプローチしています。  
ライブラリ導入時にほとんどの項目<sup>[1](#footnote1){:title="データベース接続先、テーブル名、カラム名、テーブル自動作成機能など" id="ref1"}</sup>を自動設定するので、手間をかけずにデータベースに接続することができます。

#### 向いていること

* 新規スモールプロジェクトで手軽にDB接続したい場合
* 簡単なCRUDが中心なアプリケーション
* 既存RailsアプリのScala移行

#### 向いていないこと

* 複雑なDB設計が必要なプロジェクト
* 手動で頻繁に生SQLを実行する必要がある場合

#### サンプルコード

* [Scastieの実行可能コード](https://scastie.scala-lang.org/N5fy1pUZRWWqq8cwLHCgeQ){:target="_blank" rel="noopener"} - H2Database Engineにデータを作成して抽出して取り出すサンプル
* [サンプルプロジェクト](https://github.com/aselab/scala-activerecord-sample){:target="_blank" rel="noopener"} - コンソール実行と、PlayFramework2.x・ScalatraでのWebアプリケーションのサンプルプログラム

<hr/>

1. <a href="#ref1" id="footnote1">^</a> データベース接続先、テーブル名、カラム名、テーブル自動作成機能など
