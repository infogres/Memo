# Docker 入門(1)

## とりあえず動かしてみる

ここでは、PostgreSQLをとりあえず動かしてみる

- [とりあえず Docker で PostgreSQL を動かす手順 - Qiita](https://qiita.com/hoto17296/items/070857971f95017b5e07)

### コンテナ実行(docker run NAME)

postgres:13-alpine という既存のイメージを実行してみる。既存イメージはDocker Hub からダウンロードされ、実行される。
（PostgreSQLバージョン13用に、軽量ディストリビューションの Alpine Linux で造られたイメージ）
```
$ docker run --rm -d \
    -p 15432:5432 \
    -v postgres-tmp:/var/lib/postgresql/data \
    -e POSTGRES_PASSWORD=secret \
    postgres:13-alpine
Unable to find image 'postgres:13-alpine' locally
13-alpine: Pulling from library/postgres
188c0c94c7c5: Already exists 
56f1d1b70e7f: Pull complete 
9b4f01476d2b: Pull complete 
354372ef1765: Pull complete 
9b2c2c47ed52: Pull complete 
4a51019c1737: Pull complete 
a73459c934ba: Pull complete 
49a02dcc4168: Pull complete 
Digest: sha256:42c1b84839db8f78bb66b3d6f300a88a8517895454df4a1cb2f101dfae609769
Status: Downloaded newer image for postgres:13-alpine
852fc59afdc8fb800a790a8033359ff8062c04da17c74d3f76f6486e2c7daafd
```
ここで、

    --rm : コンテナが停止したときに、自動でコンテナを削除する
    -d(--detach) : バックグラウンドで実行
    -p 15432:5432 : コンテナ内の 5432 ポートをホストの 15432 に繋ぐ
    -v postgres-tmp:/var/lib/postgresql/data : ホストのボリューム postgres-tmp をコンテナ内の /var/lib/postgresql/data にマウントする
        （ホストのボリュームは /var/lib/docker/volumes/postgres-tmp/ 下に作られる）


### ホスト上クライアントから接続(psql)

ホスト上のpsql クライアントコマンドでpostgresサーバに接続してみる（コンテナ起動時に指定したパスワードを求められる）
```
$ psql -h localhost -p 15432 -U postgres postgres
Password for user postgres: 
psql (10.15 (Ubuntu 10.15-0ubuntu0.18.04.1), server 13.1)
WARNING: psql major version 10, server major version 13.
         Some psql features might not work.
Type "help" for help.

postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)

postgres=# 

```
※ ここで、パスワードを聞かれない場合は、指定したボリュームがパスワード無しで作成されている可能性がある。また、別のPostgreSQLバージョンで作成されたボリュームが残っていいる場合は、コンテナの起動に失敗している。


### 実行中コンテナのクライアントから接続(docker exec -it NAME psql)

コンテナ内のpsqlクライアントを使ってpostgresサーバに接続する場合は docker exec コマンドを使う。
```
$ docker exec -it heuristic_payne psql -U postgres
psql (13.1)
Type "help" for help.

postgres=# 

```
### コンテナの状態確認

コンテナの状態を確認するため、プロセス(docker ps)とイメージ(docker image ls)とボリューム(docker vlume ls)を調べてみる.


### 実行プロセスの確認(docker ps)

実行中のDockerコンテナのプロセスの状態を確認できる

```
$ docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                     NAMES
852fc59afdc8        postgres:13-alpine   "docker-entrypoint.s\u2026"   30 seconds ago      Up 28 seconds       0.0.0.0:15432->5432/tcp   heuristic_payne
```
※ここでは、名前を指定しないで実行したため、"heuristic_payne" というコンテナ名が勝手につけられている。


### イメージの確認(docker image ls)

コンテナを実行した際にダウンロードしたDockerコンテナのイメージのリストを表示できる。

```
$ docker image ls postgres
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
postgres            13-alpine           770f846cacce        3 weeks ago         159MB
```
※ここでは、最初に実行したときに Docker Hub の公開リポジトリからダウンロードした、13-alpine というタグの付いた postgres という名前のイメージが保存されている（postgres というリポジトリに 13-alpine というタグで登録されている）ことがわかる。


### ボリュームの確認(docker volume ls)

Dockerコンテナのボリュームのリストを表示できる。ボリュームは実行時に (-v) オプションで指定した名前で、存在しなければ作成される。

```
$ docker volume ls
DRIVER              VOLUME NAME
local               postgres-tmp

```
※ここでは、postgres-tmp という名前のボリュームが存在することがわかる。


### イメージの詳細確認(docker image inspect IMAGE)

Docker のイメージについては inspect サブコマンドでさらに詳しく見ることができる
```
$ docker image inspect postgres:13-alpine
[
    {
        "Id": "sha256:770f846caccea499131403039925f7b71078985f92c4cdc5706604fe87dbc665",
        "RepoTags": [
            "postgres:13-alpine"
        ],
        "RepoDigests": [
            "postgres@sha256:42c1b84839db8f78bb66b3d6f300a88a8517895454df4a1cb2f101dfae609769"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2020-11-13T02:28:13.987212778Z",
        "Container": "16c99b5294ae91827dec6f12d6db947165f4336af54bb6b0e99fd09b9516e607",
        "ContainerConfig": {
            "Hostname": "16c99b5294ae",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "5432/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=en_US.utf8",
                "PG_MAJOR=13",
                "PG_VERSION=13.1",
                "PG_SHA256=12345c83b89aa29808568977f5200d6da00f88a035517f925293355432ffe61f",
                "PGDATA=/var/lib/postgresql/data"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"postgres\"]"
            ],
            "Image": "sha256:67ba1cf24ff224506a8c2184705862752fc5b68d8f76151628b202ac129d4996",
            "Volumes": {
                "/var/lib/postgresql/data": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGINT"
        },
        "DockerVersion": "19.03.12",
…以下省略
```

### ボリュームの詳細確認(docker volume inspect VOLUME)

Docker ボリュームについても inspect サブコマンドでさらに詳しく見ることができる。
（マウントポイントも表示される）
```
$ docker volume inspect postgres-tmp
[
    {
        "CreatedAt": "2020-12-04T13:25:44+09:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/postgres-tmp/_data",
        "Name": "postgres-tmp",
        "Options": null,
        "Scope": "local"
    }
]
```

Mountpoint の情報を見てみると、PostgreSQLのデータベースのインスタンスが実際に作成されていることがわかる。

```
$ sudo ls -l /var/lib/docker/volumes/postgres-tmp/_data
total 124
-rw------- 1 70 70     3 Dec  4 13:25 PG_VERSION
drwx------ 5 70 70  4096 Dec  4 13:25 base
drwx------ 2 70 70  4096 Dec  4 14:08 global
drwx------ 2 70 70  4096 Dec  4 13:25 pg_commit_ts
drwx------ 2 70 70  4096 Dec  4 13:25 pg_dynshmem
-rw------- 1 70 70  4888 Dec  4 13:25 pg_hba.conf
-rw------- 1 70 70  1636 Dec  4 13:25 pg_ident.conf
drwx------ 4 70 70  4096 Dec  4 13:30 pg_logical
drwx------ 4 70 70  4096 Dec  4 13:25 pg_multixact
drwx------ 2 70 70  4096 Dec  4 13:25 pg_notify
drwx------ 2 70 70  4096 Dec  4 13:25 pg_replslot
drwx------ 2 70 70  4096 Dec  4 13:25 pg_serial
drwx------ 2 70 70  4096 Dec  4 13:25 pg_snapshots
drwx------ 2 70 70  4096 Dec  4 13:25 pg_stat
drwx------ 2 70 70  4096 Dec  4 14:11 pg_stat_tmp
drwx------ 2 70 70  4096 Dec  4 13:25 pg_subtrans
drwx------ 2 70 70  4096 Dec  4 13:25 pg_tblspc
drwx------ 2 70 70  4096 Dec  4 13:25 pg_twophase
drwx------ 3 70 70  4096 Dec  4 13:25 pg_wal
drwx------ 2 70 70  4096 Dec  4 13:25 pg_xact
-rw------- 1 70 70    88 Dec  4 13:25 postgresql.auto.conf
-rw------- 1 70 70 27926 Dec  4 13:25 postgresql.conf
-rw------- 1 70 70    24 Dec  4 13:25 postmaster.opts
-rw------- 1 70 70    94 Dec  4 13:25 postmaster.pid
```

