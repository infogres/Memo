# Timescale DB

（2019-03-02 JPUG合宿2019草津にて）

[Timescale \| an open-source time-series SQL database optimized for fast ingest, complex queries and scale.](https://www.timescale.com/)

## Windows版インストール

ここでは、BigSQLのPostgreSQl 11のWindows版（PostgreSQL-11.2-1-win64-bigsql.exe）があらかじめインストールされている環境で試した。

[TimescaleDB Developer Docs](https://docs.timescale.com/v1.2/getting-started/installation/windows/installation-windows)
を開き、PostgreSQLのバージョン（ここでは11）を指定し、""Install method"の"Installer (.zip)"をクリックすると、timescaledb-postgresql-11_1.2.1-windows-amd64.zipが得られる。

timescaledb-postgresql-11_1.2.1-windows-amd64.zipを展開したフォルダにある"setup.exe"を実行すると、次のようにプロンプトが表示されるので、"y"を入力する。postgresql.confの場所を聞かれるので、それ（ここでは、C:\PostgreSQL\data\pg11\postgresql.conf’）を指定し(1)、その後順次パラメータについての質問に答えて（ここではすべてデフォルトのまま"y"）、最後にEnterキーを押すと終了する。

~~~
2019/03/02 14:48:13 WELCOME to TimescaleDB Windows installer!
2019/03/02 14:48:13 timescaledb-tune is a program that modifies your postgresql.conf configuration to be optimized for your machine's resources.

Do you want to run timescaledb-tune.exe now? [(y)es / (n)o]: y
Please enter the path to your postgresql.conf:
C:\PostgreSQL\data\pg11\postgresql.conf
== Using postgresql.conf at this path:
C:\PostgreSQL\data\pg11\postgresql.conf

== Writing backup to:
C:\Users\kuwam\AppData\Local\Temp/timescaledb_tune.backup201903021523

== shared_preload_libraries needs to be updated
== Current:
#shared_preload_libraries = ''

== Recommended:
shared_preload_libraries = 'timescaledb'
-- Is this okay? [(y)es/(n)o]: y
2019/03/02 14:48:13 WELCOME to TimescaleDB Windows installer!
2019/03/02 14:48:13 timescaledb-tune is a program that modifies your postgresql.conf configuration to be optimized for your machine's resources.
Do you want to run timescaledb-tune.exe now? [(y)es / (n)o]: y

Please enter the path to your postgresql.conf:
C:\PostgreSQL\data\pg11\postgresql.conf
== Using postgresql.conf at this path:
C:\PostgreSQL\data\pg11\postgresql.conf

== Writing backup to:
C:\Users\kuwam\AppData\Local\Temp/timescaledb_tune.backup201903021523

== shared_preload_libraries needs to be updated
== Current:
#shared_preload_libraries = ''
== Recommended:

shared_preload_libraries = 'timescaledb'
-- Is this okay? [(y)es/(n)o]: y
== Recommendations based on 7.91 GB of available memory and 4 CPUs for PostgreSQL 11

== Memory settings recommendations
== Current:
shared_buffers = 128MB
#effective_cache_size = 4GB
maintenance_work_mem = 64MB
#work_mem = 4MB
== Recommended:
shared_buffers = 512MB
effective_cache_size = 6075MB
maintenance_work_mem = 1036839kB
work_mem = 12950kB
-- Is this okay? [(y)es/(s)kip/(q)uit]: y
SUCCESS: memory settings will be updated

== Parallelism settings recommendations
== Current:
MISSING: timescaledb.max_background_workers
#max_worker_processes = 8
#max_parallel_workers_per_gather = 2
#max_parallel_workers = 8
== Recommended:
timescaledb.max_background_workers = 4
max_worker_processes = 11
max_parallel_workers_per_gather = 2
max_parallel_workers = 4
-- Is this okay? [(y)es/(s)kip/(q)uit]: y
SUCCESS: parallelism settings will be updated

== WAL settings recommendations
== Current:
#wal_buffers = -1
min_wal_size = 80MB
max_wal_size = 1GB
== Recommended:
wal_buffers = 16MB
min_wal_size = 4GB
max_wal_size = 8GB

-- Is this okay? [(y)es/(s)kip/(q)uit]: y
SUCCESS: WAL settings will be updated

== Miscellaneous settings recommendations
== Current:
#default_statistics_target = 100
#random_page_cost = 4.0
#checkpoint_completion_target = 0.5
max_connections = 100
#max_locks_per_transaction = 64
== Recommended:
default_statistics_target = 500
random_page_cost = 1.1
checkpoint_completion_target = 0.9
max_connections = 50
max_locks_per_transaction = 64
-- Is this okay? [(y)es/(s)kip/(q)uit]: y
SUCCESS: miscellaneous settings will be updated
== Saving changes to: C:\PostgreSQL\data\pg11\postgresql.conf
2019/03/02 15:33:12 Installing TimescaleDB library files...
2019/03/02 15:33:12 Success!
2019/03/02 15:33:12 Installing TimescaleDB control file...
2019/03/02 15:33:12 Success!
2019/03/02 15:33:12 Installing TimescaleDB SQL files...
2019/03/02 15:33:13 Success!
TimescaleDB installation completed successfully.
Press ENTER/Return key to close...
~~~

(1)postgresql.confは次のように"psql"コマンドを実行して調べられる。
~~~
psql -c "SHOW config_file;"
~~~
postgresql.conf が更新されたことと、次の設定が有効になっていることを確認する。
~~~
shared_preload_libraries = 'timescaledb'
~~~

念のため、pkglibdirの場所を調べ、そこに拡張パッケージのライブラリファイル（ここでは、timescale*.dllという名前）の存在を確認する。

~~~
C:\WINDOWS\system32>pg_config --pkglibdir --libdir
C:/POSTGR~1/pg11/lib/POSTGR~1
C:/POSTGR~1/pg11/lib
~~~
ない場合は、インストールされた場所を探し（ここではlibdirディレクトリにインストールされていた）ので、それらのファイルをpkglibdirディレクトリに移動する。

PostgreSQL のサービスを再起動する。

管理者モードで"services.msc"を起動し、PostgreSQL サービスを開いて、「停止」と「開始」をする。
あるいは、管理者モードでコマンドプロンプト("cmd.exe")を起動し、次のようにnetコマンドを実行してもよい。）

~~~
C:\WINDOWS\system32>net stop "PostgreSQL 11.0-1 Server"
PostgreSQL 11.0-1 Server サービスを停止中です.
PostgreSQL 11.0-1 Server サービスは正常に停止されました。

C:\WINDOWS\system32>net start "PostgreSQL 11.0-1 Server"
PostgreSQL 11.0-1 Server サービスを開始します.
PostgreSQL 11.0-1 Server サービスは正常に開始されました。
~~~

次に、sharedirの場所を調べ、その下のextensionサブディレクトリに拡張ライブラリ登録用のファイル（ここでは、timescaledb.controk および、timescaledb*.sql といったファイル名）の存在を確認する。

~~~
C:\WINDOWS\system32>pg_config --sharedir
C:/POSTGR~1/pg11/share/POSTGR~1
C:\WINDOWS\system32>dir C:\POSTGR~1\pg11\share\POSTGR~1\extension\timescaledb*
…
~~~

テスト用のtutorialデータベースを作成し、tutorialデータベースに接続し、psql で、拡張パッケージの登録をする。

~~~
-- Create the database, let's call it 'tutorial'
CREATE database tutorial;

-- Connect to the database
\c tutorial

-- Extend the database with TimescaleDB
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
~~~
ない場合は次のようなエラーとなるので、
~~~
ERROR: extension "timescaledb" has no installation script nor update path for version "1.2.0"
~~~
インストールされた場所を探し（ここではsharedirにインストールされていた）ので、それらのファイルをextensionサブディレクトリに移動する。

-----

ちなみに、pg_configでPostgreSQLのインストール状態を確認
~~~
C:\WINDOWS\system32>pg_config
BINDIR = C:/POSTGR~1/pg11/bin
DOCDIR = C:/POSTGR~1/pg11/share/doc/POSTGR~1
HTMLDIR = C:/POSTGR~1/pg11/share/doc/POSTGR~1
INCLUDEDIR = C:/POSTGR~1/pg11/include
PKGINCLUDEDIR = C:/POSTGR~1/pg11/include/POSTGR~1
INCLUDEDIR-SERVER = C:/POSTGR~1/pg11/include/POSTGR~1/server
LIBDIR = C:/POSTGR~1/pg11/lib
PKGLIBDIR = C:/POSTGR~1/pg11/lib/POSTGR~1
LOCALEDIR = C:/POSTGR~1/pg11/share/locale
MANDIR = C:/PostgreSQL/pg11/share/man
SHAREDIR = C:/POSTGR~1/pg11/share/POSTGR~1
SYSCONFDIR = C:/PostgreSQL/pg11/etc/postgresql
PGXS = C:/POSTGR~1/pg11/lib/POSTGR~1/pgxs/src/MAKEFI~1/pgxs.mk
CONFIGURE = '--prefix=C:/msys64/opt/pgbin-build/pgbin/bin/../20190213_0835/build/pg11-11.2-1-win64' '--enable-integer-datetimes' '--enable-thread-safety' '--with-libxml' '--with-libxslt' '--with-ossp-uuid' '--enable-nls' '--with-openssl' '--with-ldap' '--with-python' '--with-perl' '--with-tcl' '--with-libraries=/opt/pgbin-build/pgbin/shared/win64/lib:/usr/local/lib:/usr/local/ssl/lib' '--with-includes=/usr/local/include:/usr/local/ssl/include' 'CFLAGS=-O2 -DMS_WIN64 -I/opt/pgbin-build/pgbin/shared/win64/include' 'PKG_CONFIG_PATH=/mingw64/lib/pkgconfig:/mingw64/share/pkgconfig'
CC = gcc
CPPFLAGS = -I./src/include/port/win32 -DEXEC_BACKEND -I/usr/local/include/libxml2 -I/usr/local/include -I/usr/local/ssl/include -I../../src/include/port/win32 -DBUILDING_DLL
CFLAGS = -Wall -Wmissing-prototypes -Wpointer-arith -Wdeclaration-after-statement -Wendif-labels -Wmissing-format-attribute -Wformat-security -fno-strict-aliasing -fwrapv -fexcess-precision=standard -O2 -DMS_WIN64 -I/opt/pgbin-build/pgbin/shared/win64/include
CFLAGS_SL =
LDFLAGS = -Wl,--allow-multiple-definition -Wl,--disable-auto-import -L/usr/local/lib -LC:/msys64/mingw64/lib -L/opt/pgbin-build/pgbin/shared/win64/lib -L/usr/local/lib -L/usr/local/ssl/lib -Wl,--as-needed
LDFLAGS_EX =
LDFLAGS_SL =
LIBS = -lpgcommon -lpgport -lintl -lxslt -lxml2 -lssl -lcrypto -lz -lws2_32 -lm  -lws2_32
VERSION = PostgreSQL 11.2
~~~
-----

## (ハイパー)テーブルの作成

TimescaleDBの（ハイパー）テーブルを作成するには、時刻歴テーブルを作成して、ハイパーテーブルにする。

ここでは、次のようなtimeカラムを時刻とする例題用のPostgreSQLの時刻歴テーブルを作成する。

~~~
-- We start by creating a regular SQL table
CREATE TABLE conditions (
  time        TIMESTAMPTZ       NOT NULL,
  location    TEXT              NOT NULL,
  temperature DOUBLE PRECISION  NULL,
  humidity    DOUBLE PRECISION  NULL
);
~~~

次に、作成したテーブルをハイパーテーブルにする。

~~~
-- This creates a hypertable that is partitioned by time
--   using the values in the `time` column.
SELECT create_hypertable('conditions', 'time');
~~~

このテーブルにデータを追加するには、通常のINSERT文を使ってできる。

~~~
INSERT INTO conditions(time, location, temperature, humidity)
  VALUES (NOW(), 'office', 70.0, 50.0);
~~~

同様に検索も可能である。

~~~
SELECT * FROM conditions ORDER BY time DESC LIMIT 100;
~~~


##  例題

[TimescaleDB Developer Docs -- samples](https://docs.timescale.com/v1.2/tutorials/other-sample-datasets)


使用例(devices_small)

~~~
 (1) unzip the archive
tar -xvzf devices_small.tar.gz

 (2) import the .sql file to the database
psql -U postgres -d devices_small < devices.sql

 (3) import data from .csv files to the database
psql -U postgres -d devices_small -c "\COPY readings FROM devices_small_readings.csv CSV"
psql -U postgres -d devices_small -c "\COPY device_info FROM devices_small_device_info.csv CSV"
~~~

これを、新たなテーブルを作成して試してみる。

まず、アーカイバ(lhaplus等)でダウンロードしたサンプルファイルを展開する。
tarコマンドを使った例：
~~~
[C:\Temp] tar -xvzf devices_small.tar.gz
~~~
（ここでは、展開先を\Temp フォルダ内とする）

~~~
[C:\Temp] dir \Temp\devices_small
 ドライブ C のボリューム ラベルは Local Disk です
 ボリューム シリアル番号は 6E24-9181 です

 \Temp\devices_small のディレクトリ

2019/03/02  22:58    ＜DIR＞          .
2019/03/02  22:58    ＜DIR＞          ..
2017/04/26  04:28               873 devices.sql
2017/04/26  03:44            33,666 devices_small_device_info.csv
2017/04/26  03:45       144,767,821 devices_small_readings.csv
               3 個のファイル         144,802,360 バイト
               2 個のディレクトリ  84,344,524,800 バイトの空き領域
               
~~~

psql を起動して以下のデータ登録作業を行う。

1)データベースを作成し、そのデータベースに接続する。
~~~
postgres=# CREATE DATABASE devices_small;
postgres=# \c devices_small;
devices_small=# \i  \Temp\devices_small\devices.sql
~~~

2)SQLファイルの内容(以下のとおり)を実行し、ハイパーテーブルを作成する。
~~~
DROP TABLE IF EXISTS "device_info";
CREATE TABLE "device_info"(
    device_id     TEXT,
    api_version   TEXT,
    manufacturer  TEXT,
    model         TEXT,
    os_name       TEXT
);

DROP TABLE IF EXISTS "readings";
CREATE TABLE "readings"(
    time  TIMESTAMP WITH TIME ZONE NOT NULL,
    device_id  TEXT,
    battery_level  DOUBLE PRECISION,
    battery_status  TEXT,
    battery_temperature  DOUBLE PRECISION,
    bssid  TEXT,
    cpu_avg_1min  DOUBLE PRECISION,
    cpu_avg_5min  DOUBLE PRECISION,
    cpu_avg_15min  DOUBLE PRECISION,
    mem_free  DOUBLE PRECISION,
    mem_used  DOUBLE PRECISION,
    rssi  DOUBLE PRECISION,
    ssid  TEXT
);
CREATE INDEX ON "readings"(time DESC);
CREATE INDEX ON "readings"(device_id, time DESC);
-- 86400000000 is in usecs and is equal to 1 day
SELECT create_hypertable('readings', 'time', chunk_time_interval => 86400000000);
~~~

3)テーブルの内容をCSVファイルからインポートする。
~~~
devices_small=# \COPY readings FROM \Temp\devices_small\devices_small_readings.csv WITH DELIMITER ',';
devices_small=# \COPY  device_info FROM \Temp\devices_small\devices_small_device_info.csv WITH DELIMITER ',';
~~~
