---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
sidebar: 4
---

# データベース接続設定

[HikariCP]: https://github.com/brettwooldridge/HikariCP
[HOCON]: https://github.com/typesafehub/config

## 実行モードの指定方法

JavaVMシステムプロパティの `run.mode` によって実行モードを指定します。DB接続設定を実行モード別に切り替えることができます。
> **備考 :** デフォルトでは `dev` を設定しています。

## データベース接続設定の定義方法

Scala ActiveRecord でデータベース接続設定を定義する場合、 クラスパス上の `application.conf` に記述します。(例: `src/main/resources/application.conf`)
> **備考 :** 設定形式は [HOCON] 形式です。

> **備考 :** 以下の例ではPostgreSQLを使用した場合の接続設定例を記載していますが、他のデータベース接続の場合の記載例につきましては [JDBCドライバの指定方法]({{ site.baseurl }}{% link docs/scala-activerecord/ja/jdbc.md %})を参照ください。

### application.conf の記載方法

`[実行モード].[設定名] = [設定値]` の形式で記述します。

```scala
dev {
  driver = "org.postgresql.Driver"
  jdbcurl = "jdbc:postgresql://hostname:5432/dbname"
  username = "user"
  password = "xxxxxxxx"
  partitionCount = 5
  maxConnectionsPerPartition = 1
  minConnectionsPerPartition = 5
  autoCreate = false
  autoDrop = false
}

test {
  driver = "org.h2.Driver"
  jdbcurl = "jdbc:h2:mem:test"
  autoCreate = true
  autoDrop = true
}
```

上記の設定例では通常PostgreSQLドライバが使用されますが、 JavaVMシステムプロパティの `run.mode` を `test` に設定した場合、H2ドライバが使用されます。

### スキーマクラスを変更する場合

デフォルトではスキーマクラス(接続先のDBのテーブル定義を管理するクラス)に `models.Tables` が使用されます。  
もしスキーマクラスを任意のパッケージ名・クラス名に変更する場合、`some.packages.SchemaTables.[実行モード].[設定名] = [設定値]` の形式のようにパッケージ名を付与して設定を記載してください。

```scala
some.packages.SchemaTables {
  dev {
    driver = "org.postgresql.Driver"
    jdbcurl = "jdbc:postgresql://hostname:5432/dbname"
    username = "user"
    password = "xxxxxxxx"
    partitionCount = 5
    maxConnectionsPerPartition = 1
    minConnectionsPerPartition = 5
    autoCreate = false
    autoDrop = false
  }

  test {
    driver = "org.h2.Driver"
    jdbcurl = "jdbc:h2:mem:test"
    autoCreate = true
    autoDrop = true
  }
}
```

### 複数のスキーマクラスを定義する場合

Scala ActiveRecordは複数のデータベース接続(それぞれ異なるDBでも良い!)をサポートしています。  
その場合、以下のように複数のスキーマ定義を `application.conf` に記載してください。

```scala
models.schema1.Schema1Tables {
  dev {
    driver = "org.postgresql.Driver"
    jdbcurl = "jdbc:postgresql://hostname:5432/dbname"
    username = "user"
    password = "xxxxxxxx"
    partitionCount = 5
    maxConnectionsPerPartition = 1
    minConnectionsPerPartition = 5
    autoCreate = false
    autoDrop = false
  }

  test {
    driver = "org.h2.Driver"
    jdbcurl = "jdbc:h2:mem:test"
    autoCreate = true
    autoDrop = true
  }
}

models.schema2.Schema2Tables {
  dev {
    driver = "org.h2.Driver"
    jdbcurl = "jdbc:h2:mem:schema2db"
    autoCreate = true
    autoDrop = true
    user=sa
    password=""
  }

  test {
    driver = "org.h2.Driver"
    jdbcurl = "jdbc:h2:mem:test"
    autoCreate = true
    autoDrop = true
  }
}
```

### 設定可能項目

主な設定可能項目は以下の通りです。
> **Note :** Scala ActiveRecord のJDBCコネクション管理は [HikariCP] で行っています。

設定名 | 型 | 説明 | デフォルト値
---------|--------|------------------|------------
driver   | 文字列 | JDBC ドライバクラス名 | `"org.h2.Driver"`
jdbcurl  | 文字列 | JDBC 接続文字列 | `"jdbc:h2:mem:activerecord"`
username | 文字列 | DB接続ユーザ名 | (なし)
password | 文字列 | DB接続パスワード | (なし)
partitionCount | 数値 | [HikariCP] の `partitionCount` <br/>コネクションプール数 | (なし)
maxConnectionsPerPartition | 数値 | [HikariCP] の `maxConnectionsPerPartition` <br/> コネクションプールごとの最大作成接続数 | (なし) 
minConnectionsPerPartition | 数値 | [HikariCP] の `minConnectionsPerPartition` <br/> コネクションプールごとの最小作成接続数| (なし)
autoCreate | 真偽値 | `Tables#initialize` で自動的にDB内にテーブルを作成するかどうか <br/> `true` の場合、接続先のDBのスキーマクラス内で定義されたテーブルが<br/> 存在しない場合、自動的に作成する。 | `true`
autoDrop | 真偽値 | `Tables#cleanup` で自動的にDB内のテーブルを削除するかどうか <br/> `true` の場合、接続先のDBのスキーマクラス内で定義されたテーブルを<br/> 自動的に削除する。 | `false`
