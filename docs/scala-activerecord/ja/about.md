---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
sidebar: 1
---

# 概要

[`Scala ActiveRecord`](https://github.com/aselab/scala-activerecord){:target="_blank" rel="noopener"}  は以下の特徴を持っています。

## Ruby on Rails の ActiveRecord のようなインターフェース

よくある Java 製 ORM ライブラリの場合、 JDBC接続するための何らかの Manager クラスを介して操作する必要が  
あるなどインターフェースが直感的ではないものが多いと思います。  
Scala ActiveRecord では Scala の ケースクラスおよびスキーマ定義と それに対応するコンパニオンオブジェクトを定義するだけで  
Ruby on Rails の ActiveRecord のようなインターフェースで DB操作が行えます。

例えば、以下のコード **例1** のような2行のモデルとコンパニオンオブジェクトを定義すると、  
コード **例3** のように `Person` モデルに対する find, save, delete などの CRUD操作が可能となります。

##### 例1 : モデル定義 `src/main/scala/models/Person.scala`

```scala
package models

import com.github.aselab.activerecord._
import com.github.aselab.activerecord.dsl._

case class Person(name: String, age: Int) extends ActiveRecord

object Person extends ActiveRecordCompanion[Person]
```

##### 例2 : スキーマ定義 `src/main/scala/models/Tables.scala`

```scala
package models

import com.github.aselab.activerecord._
import com.github.aselab.activerecord.dsl._

object Tables extends ActiveRecordTables {
  val people = table[Person]
}
```

##### 例3: モデルを使用したDB操作 `src/main/scala/App.scala`

```scala
import com.github.aselab.activerecord.dsl._
import models._
import scala.language.postfixOps

object App extends App {
  // DBへの保存処理
  Person("Alice", 43).save
  Person("Bob", 37).save
  Person("Eve", 43).save
  Person("Steve", 35).save

  // DBへの参照処理
  Person.findBy("name", "Alice")
  //=> Some(Person("Alice", 43)) が返る
  Person.findAllBy("age", 43).toList
  //=> List(Person("Alice", 43), Person("Eve", 43)) が返る
  Person.where(_.age.~ <= 40).orderBy(_.age desc).toList
  //=> List(Person("Bob", 37), Person("Steve", 35)) が返る
}
```

上記につきまして [Scastieの実行可能コード](https://scastie.scala-lang.org/N5fy1pUZRWWqq8cwLHCgeQ){:target="_blank" rel="noopener"} で実際にブラウザ上で動作検証することができますので、ぜひご確認ください。

## XMLレスな O/R マッピング

Java の DB操作ライブラリにありがちな 大量の XML ファイルによる設定ファイルを作成することなく使用できます。  
設定ファイルも1つのみで済むシンプルなものとなっています。

## DB接続に関するコードがほぼ不要

設定ファイルに接続する データベースの情報（URL・ユーザ名・パスワードなど）を書いておくだけで  
コネクションプールを介した データベース接続を行うことが可能です。  
また、設定が書かれていない場合はデフォルト値が用意されているなど、 [CoC](https://ja.wikipedia.org/wiki/%E8%A8%AD%E5%AE%9A%E3%82%88%E3%82%8A%E8%A6%8F%E7%B4%84){:target="_blank" rel="noopener"} の考え方に則っています。
