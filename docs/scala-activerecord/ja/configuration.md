---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
sidebar: 2
---

# 導入方法

[`Scala ActiveRecord`](https://github.com/aselab/scala-activerecord){:target="_blank" rel="noopener"} を使用するための依存関係解決方法などについて記載します。

## Simple Build Tool (SBT) の場合

`build.sbt` または `project/Build.scala` に以下のように依存ライブラリ定義を追加します：
> **備考 :** SLF4J と JDBC driver ライブラリはオプションの指定となります。環境に応じて変更してください。

> **参考情報 :** [`サンプルプロジェクトのbuild.sbt`](https://github.com/aselab/scala-activerecord-sample/blob/master/sample/build.sbt){:target="_blank" rel="noopener"} もご参照ください。

```scala
libraryDependencies ++= Seq(
  "com.github.aselab" %% "scala-activerecord" % "0.6.2",
  "org.slf4j" % "slf4j-nop" % "1.7.30",                   // slf4j-simple, logback-classicなどの任意のロガーを指定
  "com.h2database" % "h2" % "1.4.200"                     // 使用したいデータベースのJDBCドライバーのjarを指定
)
```

## Maven の場合

`pom.xml` に以下のような設定を記載してください。

> **備考 :** SLF4J と JDBC driver ライブラリはオプションの指定となります。環境に応じて変更してください。

```xml
<dependencies>
  <dependency>
    <groupId>com.github.aselab</groupId>
    <artifactId>scala-activerecord_2.13</artifactId>
    <version>0.6.2</version>
  </dependency>
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-nop</artifactId>
    <version>1.7.30</version>
  </dependency>
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>1.4.200</version>
  </dependency>
</dependencies>
```
