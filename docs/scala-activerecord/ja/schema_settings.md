---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
sidebar: 5
---

# スキーマ設定

## スキーマクラス models.Tables の定義

スキーマクラスでは、データベース上のテーブル名とScalaのモデルクラスとのマッピングを設定します。  
定義する箇所はデフォルトでは `models.Tables` オブジェクトとなります。(変更・追加する場合は [データベース接続設定]({{ site.baseurl }}{% link docs/scala-activerecord/ja/database_settings.md %})の設定方法を参照ください)

スキーマ定義は `com.github.aselab.activerecord.ActiveRecordTables` を継承したオブジェクト上に定義します。  
モデルとテーブルのマッピングは、 `table` メソッドで`com.github.aselab.activerecord.ActiveRecord` の型を継承した  
モデルを指定した定数を定義することで行われます。（定数名は何でも構いません）  

以下の **例** は `Person` モデルのスキーマ定義のサンプルです。

> **備考 :**  デフォルトではモデルと対応するテーブル名はモデルクラス名を小文字化してアンダースコア区切りの複数形にしたものとなります。<br/> (例: `UserAccount` モデルの場合、`user_accounts`) <br/> 引数で指定すると任意のテーブル名に変更することが可能です。

#### 例: スキーマ定義 `src/main/scala/models/Tables.scala`

```scala
package models

import com.github.aselab.activerecord._

object Tables extends ActiveRecordTables {
  val people = table[Person]
  // テーブル名を任意のものに変更したい場合
  // val people = table[Person]("people")
}
```

## テーブル接続ルール設定

### デフォルトの命名規則ルール

 タイプ | 命名規則 | 例
---------|----------------------|------------
テーブル名 | モデルクラス名を小文字化してアンダースコア区切りの複数形にしたもの | UserAccount => user_accounts
中間テーブル名 | テーブル名をアルファベット順にソートして アンダースコア区切りの複数形にしたもの | User, Group => groups_users
変数名とDBカラム名の対応 | Scalaモデルの変数名をアンダースコア区切りにしたもの | fieldName => field_name
外部キー用変数名 | 関連モデル名のキャメルケース + "Id" | ModelClass => modelClassId

### 命名規則ルールの設定上書き

デフォルトでは上記に示すようにScala ActiveRecordのテーブル名・カラム名命名規則はRuby on RailsのActiveRecordの規約に沿った形が基本となっています。  
上記の動作は `com.github.aselab.activerecord.ActiveRecordTables` のメソッドをスキーマ定義時にオーバーライドすることで設定変更が可能です。

#### 例: 命名規則上書き `src/main/scala/models/Tables.scala`

```scala
import com.github.aselab.activerecord._
import com.github.aselab.activerecord.dsl._

object Tables extends ActiveRecordTables {
  // テーブル名の命名規則を ModelClass => "MODEL_CLASS" のように変更する場合
  override def tableNameFromClass(c: Class[_]): String =
    c.getSimpleName.underscore.toUpperCase

  // 中間テーブル名の命名規則を (ModelClass1, ModelClass2) => "MODEL_CLASS1___MODEL_CLASS2" のように変更する場合
  override def tableNameFromClasses(c1: Class[_], c2: Class[_]): String =
    Seq(c1, c2).map(tableNameFromClass).sorted.mkString("___")

  // 変数名とDBカラム名の対応の規則を ModelField => "MODEL_FIELD" に変更する場合
  override def columnNameFromPropertyName(propertyName: String): String  =
     propertyName.underscore.toUpperCase

  // 外部キー用変数の命名規則を ModelClass => "modelClass_id" に変更する場合
  override def foreignKeyFromClass(c: Class[_]): String =
    c.getSimpleName.camelize + "_id"
}
```
