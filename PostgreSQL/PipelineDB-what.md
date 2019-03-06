# PipelineDB what?

* 連続的なSQLクエリにより高性能な時系列集約処理を行う。
* 集約データが必要なリアルタイムレポートのようなところに威力を発揮する。
* PipelineDB 1.0 は、PostgreSQL の拡張モジュールとして実装された。
* 現在(2016-10) PostgreSQL 10 (10.1, 10.2, 10.3, 10.4 10.5)をサポート。([Releases · pipelinedb/pipelinedb · GitHub](https://github.com/pipelinedb/pipelinedb/releases))
* Apache 2.0 ライセンス

## インストール

apt と yum のリポジトリが用意されている。

### apt

    curl -s http://download.pipelinedb.com/apt.sh | sudo bash
    sudo apt-get install pipelinedb-postgresql-10

### yum

    curl -s http://download.pipelinedb.com/yum.sh | sudo bash
    sudo yum install pipelinedb-postgresql-10

## PipelineDBの構成

＄PGDATA/postgresql.conf を編集し、次の行を追加する。
```
# At the bottom of <data directory>/postgresql.conf
shared_preload_libraries = 'pipelinedb'
max_worker_processes = 128
```

PipelineDBを使いたいデータベースにアクセスし、拡張機能として有効にする。

    psql -c "CREATE EXTENSION pipelinedb"

## その他

使い方については、[PipelineDB Documentation - PipelineDB 1.0.0](http://docs.pipelinedb.com/)　を参照のこと。
日本国内での導入例は、「[PipelineDBをProductionに導入してみました。 How SmartNews Utilizes PipelineDB - SmartNews Engineering Blog](https://developer.smartnews.com/blog/2015/09/09/20150907pipelinedb/)」を参照のこと。

