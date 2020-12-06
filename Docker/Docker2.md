# Docker 入門(2)

## Docker コンテナ

### Docker Container 操作コマンド

- [Docker container コマンドのメモ（コマンドフロー図付き） - Qiita](https://qiita.com/gounx2/items/c63a04f36c4bfd9621ff)

docker container コマンドのcontainer は省略可。
（例 docker container run は docker run ）
コンテナのライフサイクル：「実行中 -> 停止 -> 破棄」

![abc30526.png](storage/212adf59-55c0-4a62-b70e-85f16bd82a48/77257b4b.png)

実際に Docker コンテナをコマンドを使って操作してみる。

#### 実行例(docker container run IMAGE)
コンテナを生成実行＆指定コマンド(/bin/bash)を実行
```
$ docker container run -it --rm --name ubuntu-bash ubuntu /bin/bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
da7391352a9b: Pull complete 
14428a6d4bcd: Pull complete 
2c2d948710f2: Pull complete 
Digest: sha256:c95a8e48bf88e9849f3e0f723d9f49fa12c5a00cfc6e60d2bc99d87555295e4c
Status: Downloaded newer image for ubuntu:latest
root@d6195f34b051:/# 
```
ここで、

    -i(--interactive) : ホストの入力をコンテナにつなぎ続ける
        ctrl+p ctrl+q : コンテナ終了せずホストに戻る
                              再度コンテナにつなぐ場合、docker container attach
    -t(--tty) : ホストシェルの入出力を実行コンテナの PID=1 に繋ぐ
    --rm : コンテナが停止したときに、自動でコンテナを削除する
    --name 名前: 任意のコンテナ名を付ける(無いと適当な名前になる)


バックグラウンドで起動して常駐（このnginx は既に実行したことがあるため既にpullしたイメージを使って起動する）
```
$ docker container run -d -p 8080:80 nginx
dedeb8961c3b7069a12f855ebfb4d6cb08bf2e0a4620fc7a9348c91d8605d614
```
ここで、

    -d(--detach) : バックグラウンドで実行

#### 実行状態(docker container ps)

実行中のコンテナをリストする(ps の代わりに lsも使える)
```
$ docker container ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
dedeb8961c3b        nginx               "/docker-entrypoint.\u2026"   4 minutes ago       Up 4 minutes        0.0.0.0:8080->80/tcp   ecstatic_franklin
d6195f34b051        ubuntu              "/bin/bash"              4 minutes ago       Up 4 minutes                               ubuntu-bash
```

#### 実行停止(docker container stop CONTAINER)

実行中のコンテナ名（コンテナIDでもよい）を指定して停止する
```
$ docker container stop ecstatic_franklin
ecstatic_franklin
```

docker container ls(psと同じ) でプロセスをリストする
( -all) を付けると停止中のプロセスも表示される。
```
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0d94575f0a9c        ubuntu              "/bin/bash"         39 minutes ago      Up 39 minutes                           ubuntu-bash

$ docker container ls --all
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
dedeb8961c3b        nginx               "/docker-entrypoint.\u2026"   39 minutes ago      Exited (0) 31 minutes ago                       ecstatic_franklin
d6195f34b051        ubuntu              "/bin/bash"              39 minutes ago      Up 39 minutes                                   ubuntu-bash

```

#### 実行継続(docker container attach CONTAINER)

実行継続後、終了
```
$ docker container attach ubuntu-bash
root@d6195f34b051:/# exit

$ docker container ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
```

#### ログ表示(docker container logs CONTAINER)

コンテナの PID=1 の標準出力(エラー出力も)が logs になる
```
$ docker container logs flamboyant_turing
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
```

tail コマンドと同様の (-f) オプションも使える

    -f(--follow) : 出力を追いかける(出力を待ち続ける)



#### 停止コンテナ削除(docker container rm CONTAINER)

バックグラウンドで実行したnginx が停止中であることを確認し、
コンテナID（コンテナ名でもよい）を指定して削除
```
$ docker container ls --all
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
dedeb8961c3b        nginx               "/docker-entrypoint.\u2026"   39 minutes ago      Exited (0) 31 minutes ago                       ecstatic_franklin

$ docker container rm dedeb8961c3b
$ docker container ls --all
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES

```
