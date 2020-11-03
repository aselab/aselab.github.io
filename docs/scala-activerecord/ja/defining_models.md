---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
sidebar: 5
---
# モデル定義方法

モデル定義とテーブルスキーマ定義を行うことでScala ActiveRecordのDB操作を行うことができます。  
本ページではモデル定義を記載します。  
実際にDB操作で使用するためには別途、[テーブルスキーマ定義方法]({{ site.baseurl }}{% link docs/scala-activerecord/ja/schema_settings.md %}) が必要となりますので合わせて参照ください。

[squeryl]: https://www.squeryl.org/docs/0.9.5/schema-definition.html
[CRUD]: https://ja.wikipedia.org/wiki/CRUD

## モデル定義方法

データベースのテーブルに対して対応するモデル定義を行うためには、ケースクラス(case class)と  
コンパニオンオブジェクト(Companion Object)の2つを定義します。

ケースクラスは `com.github.aselab.activerecord.ActiveRecord` クラスを継承したものを定義します。  
`ActiveRecord` クラスを継承することで、テーブル内のレコードに対応した[CRUD]ロジックなどが提供されます。

コンパニオンオブジェクトは、ケースクラスのモデルの型 `T` をパラメータに取る `com.github.aselab.activerecord.ActiveRecordCompanion[T]` を継承したものを定義します。  
`ActiveRecordCompanion[T]` を継承することでテーブルに対応したクエリロジックなどが提供されます。

#### 例: Userモデル定義 `src/main/scala/models/User.scala`

```scala
package models

import com.github.aselab.activerecord._

case class User(var name: String, var age: Int) extends ActiveRecord

object User extends ActiveRecordCompanion[User]
```

> **備考 :** 上記のモデル定義に対応するデータベース上の `users` テーブル定義例は以下となります。  
> モデル操作を行いたい場合はあらかじめDBスキーマ定義を行ってください。
> ```sql
> create table users (
>  id bigint not null primary key auto_increment,
>  name varchar(128) not null,
>  age int not null
> );
> ```


### モデル変数からDBカラムへのマッピング

デフォルトの状態では、ケースクラス(モデル)の各フィールド変数はデータベース内のカラムにマッピングされます。  
カラム名のマッピングはすべて小文字になりアンダースコアで区切られる形式となります。  
（例： ケースクラスの `createdDate` フィールド は `created_date` カラムにマッピングされます）

`com.github.aselab.activerecord.dsl.Column` アノテーションをフィールド変数定義で指定することで任意のカラム名にマッピングすることが可能です。  

#### 例: カラム名変更 `src/main/scala/models/Group.scala`

以下の場合、 `createDate` フィールドは `groups` テーブルの `CreatedDate` カラムと対応します。

```scala
package models

import com.github.aselab.activerecord._
import com.github.aselab.activerecord.dsl._
import java.util.Date

case class Group(
  var name: String,
  @Column("CreatedDate") createdDate: Date
) extends ActiveRecord

object Group extends ActiveRecordCompanion[Group]
```

### `@Transient` アノテーション

DBのテーブル上で管理したくないフィールド変数を定義する場合は `com.github.aselab.activerecord.dsl.Transient` アノテーションを付与してください。

```scala
package models

import com.github.aselab.activerecord._
import com.github.aselab.activerecord.dsl._

case class User(
  var name: String,
) extends ActiveRecord {
  @Transient var fieldName = "この変数はDBに保存されません"
}

object User extends ActiveRecordCompanion[User]
```

### Nullを許容するカラムの場合(`Option` 型)

> **備考 :** ラップ元のライブラリ [squeryl] での使用方法と同じとなります。

Nullを許容するカラムを定義する場合、`Option`型で包んで型を定義してください。  
Scala ActiveRecordで自動的に生成するテーブルスキーマ定義では、`Option`型でない場合はNot Null制約が付与されます。

```scala
package models

import com.github.aselab.activerecord._
import com.github.aselab.activerecord.dsl._

case class Foo(var foo: String) extends ActiveRecord

object Foo extends ActiveRecordCompanion[Foo]

case class Bar(var bar: Option[String]) extends ActiveRecord

object Bar extends ActiveRecordCompanion[Bar]

...

Foo(null).save()  // => 'NULL not allowed for column "FOO";' といったDBのNot Null制約が発生し実行時例外が発生する
Bar(None).save()  // => OK
```

### モデルで使用可能な型

ケースクラス内でDBテーブルとマッピング可能な型は以下の通りです。

- `String`
- `Boolean`
- `Int`
- `Long`
- `Float`
- `Double`
- `BigDecimal`
- `java.sql.Timestamp`
- `java.util.Date`
- `java.util.UUID`

## 自動タイムスタンプ付与

### Timestampsトレイト

モデル定義時に `Timestamps` トレイトをmixinすると、データ作成時と更新時に自動的にタイムスタンプ(日時)を作成/更新します。  
(DBテーブル定義では`created_at` と `updated_at` カラムを追加してください。 モデルでは `createdAt`, `updatedAt` 変数が追加されます)

```scala
case class Foo(var foo: String) extends ActiveRecord with Timestamps

object Foo extends ActiveRecordCompanion[Foo]

// val foo = Foo("foo").create()
// foo.createdAt // => created Timestamp
// foo.updatedAt // => updated Timestamp
```

### Datestampsトレイト

モデル定義時に `Datestamps` トレイトをmixinすると、データ作成時と更新時に自動的にタイムスタンプ(日付)を作成/更新します。  
(DBテーブル定義では`created_on` と `updated_on` カラムを追加してください。 モデルでは `createdOn`, `updatedOn` 変数が追加されます)

```scala
case class Foo(var foo: String) extends ActiveRecord with Datestamps

object Foo extends ActiveRecordCompanion[Foo]

// val foo = Foo("foo").create()
// foo.createdOn // => created Date
// foo.updatedOn // => updated Date
```

## 楽観的ロック

モデル定義時に `Optimistic` トレイトをmixinすると楽観的ロックが自動的に行われるようになります。  
モデルのインスタンスで`save`による更新が行われると、`occVersionNumber` 変数による楽観的ロック制御が行われ、  
テーブル上の `occ_version_number` カラムの値が +1 増加されるようになります。  
楽観的ロック実行時に複数のインスタンスによる保存が行われた場合（`occ_version_number` の値が変更前に他者に変更されていると検知した場合）、  
`StaleObjectException` 例外をスローします。

#### 例: 楽観的ロックの適用実装例

```scala
case class Book(var name: String, var price: Int) extends ActiveRecord with Optimistic

object Book extends ActiveRecordCompanion[Book]

val b1 = Book.head
val b2 = Book.head
b1.name = "update"
b1.save
b2.name = "other update"
b2.save  // throws com.github.aselab.activerecord.StaleObjectException
```

この楽観的ロック機能はレコード削除時も検知します。

```scala
val b1 = Book.head
val b2 = Book.head
b1.name = "update"
b1.save
b2.delete  // throws com.github.aselab.activerecord.StaleObjectException
```
