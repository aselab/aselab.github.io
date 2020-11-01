---
layout: sidebar
site-title: Scala ActiveRecord ドキュメント
lang: ja-JP
base-url: '/docs/scala-activerecord/ja/'
sidebar: 3
---

# JDBCドライバの指定方法

[導入方法]({{ site.baseurl }}{% link docs/scala-activerecord/ja/configuration.md %}) の中でJDBCドライバを指定する際の依存関係指定方法(例)と、データベース設定記載方法(例)を記載します。  
また、Scala ActiveRecordでサポートしているデータベースは以下に記載しているものとなります。

## H2 (デフォルト)

SBT を使用している場合、`build.sbt`の`libraryDependencies`に以下を指定してください。

> **備考 :** 何も依存関係とデータベース設定を明示的に設定していない場合、H2のJDBCドライバおよびデータベースが自動的に使用されます。

```scala
"com.h2database" % "h2" % "1.4.200"
```

[[Database Settings]]:
```
driver = org.h2.Driver
jdbcurl = jdbc:h2:mem:activerecord
```

## MySQL

SBT を使用している場合、`build.sbt`の`libraryDependencies`に以下を指定してください。

```scala
"mysql" % "mysql-connector-java" % "8.0.20"
```

[[Database Settings]]:
```
driver = com.mysql.jdbc.Driver
jdbcurl = jdbc:mysql://hostname:3306/dbname
username = <username>
password = <password>
```

## PostgreSQL

SBT を使用している場合、`build.sbt`の`libraryDependencies`に以下を指定してください。

```scala
"org.postgresql" % "postgresql" % "42.2.14"
```

[[Database Settings]]:
```
driver = org.postgresql.Driver
jdbcurl = jdbc:postgresql://hostname:5432/dbname
username = <username>
password = <password>
```

## Derby

SBT を使用している場合、`build.sbt`の`libraryDependencies`に以下を指定してください。

```scala
"org.apache.derby" % "derby" % "10.15.2.0"
```

[[Database Settings]]:
```
driver = org.apache.derby.jdbc.EmbeddedDriver
jdbcurl = jdbc:derby:memory:dbname;create=true
```

## Oracle

Oracle JDBCドライバを手動でダウンロードし依存関係に追加してください。

[[Database Settings]]:
```
driver = oracle.jdbc.OracleDriver
jdbcurl = jdbc:oracle:<drivertype>:@<hostname>:<port>:<database>
```

## MSSQL

MSSQL JDBCドライバを手動でダウンロードし依存関係に追加してください。

[[Database Settings]]:
```
driver = com.microsoft.sqlserver.jdbc.SQLServerDriver
jdbcurl = jdbc:sqlserver://<hostname>:<port>;DatabaseName=<database>
```

## DB2

DB2 JDBCドライバを手動でダウンロードし依存関係に追加してください。

[[Database Settings]]:
```
driver = com.ibm.db2.jcc.DB2Driver
jdbcurl = jdbc:db2@<hostname>:<port>/<database>
```

## SQLite

SBT を使用している場合、`build.sbt`の`libraryDependencies`に以下を指定してください。

```scala
"org.xerial" % "sqlite-jdbc" % "3.32.3"
```

[[Database Settings]]:
```
driver = org.sqlite.JDBC
jdbcurl = jdbc:sqlite:sample.db
```

