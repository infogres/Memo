# Docker 入門

Ubuntu 18.04 LTS に docker をインストールして試してみた。
インストールについては次を参照のこと。

- [Ubuntu 18.04にDockerをインストールする(+docker-composeも) - Qiita](https://qiita.com/iganari/items/fe4889943f22fd63692a)
- [Install Docker Engine on Ubuntu \| Docker Documentation](https://docs.docker.com/engine/install/ubuntu/)


## Docker コンテナとは

- [Docker入門（第一回）～Dockerとは何か、何が良いのか～ \| さくらのナレッジ](https://knowledge.sakura.ad.jp/13265/)

Dockerは Linuxのコンテナ技術を使い、プロセスやユーザなどを隔離することで、あたかも別のマシンが動いているかのように、動作環境を独立させて動かすことができる。

仮想マシンと比較すると、
仮想マシンでは、ホストマシン上でハイパーバイザを利用し、その上でゲストOSを立ち上げて、そこでミドルウェアなどを動かす。それに対してコンテナは、ホストマシンのカーネルを利用し、プロセスやユーザなどを隔離することで、あたかも別のマシンが動いているかのように動かす。

コンテナの特徴は、仮想マシンに比べて軽量で高速に起動、停止などが可能。

![6398d274.png](storage/a2ae1c41-ed8a-45f4-b752-2964a00788ce/6398d274.png)


- [Docker container コマンドのメモ（コマンドフロー図付き） - Qiita](https://qiita.com/gounx2/items/c63a04f36c4bfd9621ff)

docker container コマンドのcontainer は省略可。
（例 docker container run は docker run ）
コンテナのライフサイクル：「実行中 -> 停止 -> 破棄」

![abc30526.png](storage/a2ae1c41-ed8a-45f4-b752-2964a00788ce/227247cf.png)


## とりあえず動かしてみる

- [とりあえず Docker で PostgreSQL を動かす手順 - Qiita](https://qiita.com/hoto17296/items/070857971f95017b5e07)


## Docker チュートリアル

- [GitHub - 7nohe/docker-lessons: Docker研修用資料](https://github.com/7nohe/docker-lessons)


## Docker のContainer 操作コマンド

- [Docker container コマンドのメモ（コマンドフロー図付き） - Qiita](https://qiita.com/gounx2/items/c63a04f36c4bfd9621ff)


## Dockerイメージ

- [ゼロからはじめるDocker(4) Dockerのコンテナイメージを作成してみよう \| マイナビニュース](https://news.mynavi.jp/article/docker-4/)
- [イメージの構築 — Docker-docs-ja 1.9.0b ドキュメント](http://docs.docker.jp/v1.9/engine/userguide/dockerimages.html)

Dockerコンテナを生成するためのもととなるのがDockerイメージで、既に用意されているものを Docker hub からダウロード (pull) することができる。
カスタマイズしたい場合は、独自に Dockerfileを定義することでも作成できる。


## Dockerネットワーク

- [Docker network 概論 - Qiita](https://qiita.com/TsutomuNakamura/items/ed046ee21caca4a2ffd9)
- [マルチホストでのDocker Container間通信 第1回: Dockerネットワークの基礎 - Uzabase Tech Blog](https://tech.uzabase.com/entry/2017/08/07/172411)

Dockerネットワークを定義することで、コンテナ間での通信ができるようになる。

![02c14355.png](a2ae1c41-ed8a-45f4-b752-2964a00788ce/02c14355.png)


## DockerでPostgreSQL

### Docker オフィシャルpostgresのについて

- [Docker Hubのpostgresの使い方](https://hub.docker.com/_/postgres)


### Dockerを使ったPostgreSQLの環境構築

- [SQLを使いこなしたい！虎の巻 #1 ~ Dockerを使ったPostgreSQLの環境構築編 ~ \| Developers.IO](https://dev.classmethod.jp/articles/how-to-master-sql-tora-no-maki-1/)

### DockerfileでPostgres環境構築

- [Docker で作る postgres 環境 \| Crudzoo](https://crudzoo.com/blog/docker-postgres)
