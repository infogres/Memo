# Docker 入門(3)

## Dockerイメージ

- [ゼロからはじめるDocker(4) Dockerのコンテナイメージを作成してみよう \| マイナビニュース](https://news.mynavi.jp/article/docker-4/)
- [イメージの構築 — Docker-docs-ja 1.9.0b ドキュメント](http://docs.docker.jp/v1.9/engine/userguide/dockerimages.html)

Dockerコンテナを生成するためのもととなるのがDockerイメージで、既に用意されているものを Docker hub からダウロード (pull) することができる。
カスタマイズしたい場合は、独自に Dockerfileを定義することでも作成できる。


### 既存 Dockerfile でイメージ作成

ここでは、既存の Dockerfile をコピーしてイメージを作成してみる。

[postgres/13/alpine at master · docker-library/postgres · GitHub](https://github.com/docker-library/postgres/tree/master/13/alpine) に Alpine Linux に子構築した PostgreSQL 13 の Dockerfile がある。ここにあるファイルをダウンロードして、自分のアカウントに push してみる。

#### Dockerfile のダウンロード

github でファイル名をクリックして、[raw] ボタンをクリックすると生のファイル内容が表示され、URL を知ることができる。

```
$ mkdir alpine-pg
$ cd alpine-pg
$ wget https://raw.githubusercontent.com/docker-library/postgres/master/13/alpine/Dockerfile
$ wget https://github.com/docker-library/postgres/blob/master/13/alpine/docker-entrypoint.sh

$ ls -l
total 20
-rw-rw-r-- 1 juk juk  6630 Dec  3 18:18 Dockerfile
-rwxr-xr-x 1 juk juk 10930 Dec  3 18:19 docker-entrypoint.sh

```

Dockerfile の内容を確認
```
$ more Dockerfile 
# vim:set ft=dockerfile:
FROM alpine:3.12

# 70 is the standard uid/gid for "postgres" in Alpine
# https://git.alpinelinux.org/aports/tree/main/postgresql/postgresql.pre-install?h=3.12-stable
RUN set -eux; \
	addgroup -g 70 -S postgres; \
	adduser -u 70 -S -D -G postgres -H -h /var/lib/postgresql -s /bin/sh postgres; \
	mkdir -p /var/lib/postgresql; \
	chown -R postgres:postgres /var/lib/postgresql

# su-exec (gosu-compatible) is installed further down

# make the "en_US.UTF-8" locale so postgres will be utf-8 enabled by default
# alpine doesn't require explicit locale-file generation
ENV LANG en_US.utf8

RUN mkdir /docker-entrypoint-initdb.d

ENV PG_MAJOR 13
...
```

docker-entrypoint.sh の内容を確認
```
$ more  docker-entrypoint.sh 
#!/usr/bin/env bash
set -Eeo pipefail
# TODO swap to -Eeuo pipefail above (after handling all potentially-unset variables)

# usage: file_env VAR [DEFAULT]
#    ie: file_env 'XYZ_DB_PASSWORD' 'example'
# (will allow for "$XYZ_DB_PASSWORD_FILE" to fill in the value of
#  "$XYZ_DB_PASSWORD" from a file, especially for Docker's secrets feature)
file_env() {
	local var="$1"
...
```

#### イメージの構築(docker image build)

Docker イメージのビルド

Dockerfile がある場所を指定してイメージ構築コマンドを実行する。
（コマンド行の最後にDockerfileが存在するディレクトリの相対パスを指定していることに注意する）

```
$ docker image build -t postgres13:alpine .
Sending build context to Docker daemon  20.99kB
Step 1/18 : FROM alpine:3.12
 ---> d6e46aa2470d
Step 2/18 : RUN set -eux; 	addgroup -g 70 -S postgres; 	adduser -u 70 -S -D -G postgres -H -h /var/lib/postgresql -s /bin/sh postgres; 	mkdir -p /var/lib/postgresql; 	chown -R postgres:postgres /var/lib/postgresql
 ---> Using cache
 ---> ceb289a3c202
Step 3/18 : ENV LANG en_US.utf8
 ---> Using cache
 ---> 4e88c5ac4948
Step 4/18 : RUN mkdir /docker-entrypoint-initdb.d
 ---> Using cache
 ---> 64f00ca65a6e
Step 5/18 : ENV PG_MAJOR 13
 ---> Using cache
 ---> 16551cd2410e
Step 6/18 : ENV PG_VERSION 13.1
 ---> Using cache
 ---> a21277f3b0ae
Step 7/18 : ENV PG_SHA256 12345c83b89aa29808568977f5200d6da00f88a035517f925293355432ffe61f
 ---> Using cache
 ---> 194c210daadf
Step 8/18 : RUN set -eux; 		wget -O postgresql.tar.bz2 "https://ftp.postgresql.org/pub/source/v$PG_VERSION/postgresql-$PG_VERSION.tar.bz2"; 	echo "$PG_SHA256 *postgresql.tar.bz2" | sha256sum -c -; 	mkdir -p /usr/src/postgresql; 	tar 		--extract 		--file postgresql.tar.bz2 		--directory /usr/src/postgresql 		--strip-components 1 	; 	rm postgresql.tar.bz2; 		apk add --no-cache --virtual .build-deps 		bison 	coreutils 		dpkg-dev dpkg 		flex 		gcc 		libc-dev 		libedit-dev 		libxml2-dev 		libxslt-dev 		linux-headers 		llvm10-dev clang g++ 		make 		openssl-dev 		perl-utils 		perl-ipc-run 		util-linux-dev 		zlib-dev 		icu-dev 	; 		cd /usr/src/postgresql; 	awk '$1 == "#define" && $2 == "DEFAULT_PGSOCKET_DIR" && $3 == "\"/tmp\"" { $3 = "\"/var/run/postgresql\""; print; next } { print }' src/include/pg_config_manual.h > src/include/pg_config_manual.h.new; 	grep '/var/run/postgresql' src/include/pg_config_manual.h.new; 	mv src/include/pg_config_manual.h.new src/include/pg_config_manual.h; 	gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)"; 	wget -O config/config.guess 'https://git.savannah.gnu.org/cgit/config.git/plain/config.guess?id=7d3d27baf8107b630586c962c057e22149653deb'; 	wget -O config/config.sub 'https://git.savannah.gnu.org/cgit/config.git/plain/config.sub?id=7d3d27baf8107b630586c962c057e22149653deb'; 	./configure 		--build="$gnuArch" 		--enable-integer-datetimes 		--enable-thread-safety 		--enable-tap-tests 		--disable-rpath 		--with-uuid=e2fs 		--with-gnu-ld 		--with-pgport=5432 		--with-system-tzdata=/usr/share/zoneinfo 		--prefix=/usr/local 		--with-includes=/usr/local/include 		--with-libraries=/usr/local/lib 				--with-openssl 		--with-libxml 		--with-libxslt 		--with-icu 		--with-llvm 	; 	make -j "$(nproc)" world; 	make install-world; 	make -C contrib install; 		runDeps="$( 		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local 			| tr ',' '\n' 			| sort -u 			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' 	)"; 	apk add --no-cache --virtual .postgresql-rundeps 		$runDeps 		bash 	su-exec 		tzdata 	; 	apk del --no-network .build-deps; 	cd /; 	rm -rf 		/usr/src/postgresql 		/usr/local/share/doc 		/usr/local/share/man 	; 		postgres --version
 ---> Using cache
 ---> a998867e5fcd
Step 9/18 : RUN sed -ri "s!^#?(listen_addresses)\s*=\s*\S+.*!\1 = '*'!" /usr/local/share/postgresql/postgresql.conf.sample
 ---> Using cache
 ---> 89bfd01380b1
Step 10/18 : RUN mkdir -p /var/run/postgresql && chown -R postgres:postgres /var/run/postgresql && chmod 2777 /var/run/postgresql
 ---> Using cache
 ---> 388a84b7611a
Step 11/18 : ENV PGDATA /var/lib/postgresql/data
 ---> Using cache
 ---> fa2b4b81baba
Step 12/18 : RUN mkdir -p "$PGDATA" && chown -R postgres:postgres "$PGDATA" && chmod 777 "$PGDATA"
 ---> Using cache
 ---> a4c2f478483a
Step 13/18 : VOLUME /var/lib/postgresql/data
 ---> Using cache
 ---> fc8420eb6de4
Step 14/18 : COPY docker-entrypoint.sh /usr/local/bin/
 ---> 00b33c70cd45
Step 15/18 : ENTRYPOINT ["docker-entrypoint.sh"]
 ---> Running in f6f2866eaf02
Removing intermediate container f6f2866eaf02
 ---> 0f978bce14ae
Step 16/18 : STOPSIGNAL SIGINT
 ---> Running in 1063710dea56
Removing intermediate container 1063710dea56
 ---> 002694e9b27b
Step 17/18 : EXPOSE 5432
 ---> Running in 392bcba7617b
Removing intermediate container 392bcba7617b
 ---> 1c516aab3104
Step 18/18 : CMD ["postgres"]
 ---> Running in 3c289485b823
Removing intermediate container 3c289485b823
 ---> a2bce220cd77
Successfully built a2bce220cd77
Successfully tagged postgres13:alpine

```
ここで、

    -t IMAGE:TAG    (イメージ名):(タグ名) 


イメージ名を指定してリストを表示する。
```
$ docker image ls postgres13
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
postgres13          alpine              a2bce220cd77        11 seconds ago      160MB
postgres13          debian              94952dd1306a        10 minutes ago      314MB

```

### イメージの公開登録(docker push)

作成したDockerイメージは Docker Hub に公開することもできる。
公開するためには、Docker Hub にアカウントが必要であり。予めサインアップしておく。

### Docker Hubへのログイン(docker login)

Docker login をすると、~/.docker/config.json というキャッシュファイルができる。

```
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docke
r.com to create one.
Username: my-login-name
Password: xxxxxxxx
WARNING! Your password will be stored unencrypted in /home/juk/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

$ ls -ltr ~/.docker
total 4
-rw------- 1 juk juk 174 Dec  5 22:29 config.json

$ cat  ~/.docker/config.json 
{
	"auths": {
		"https://index.docker.io/v1/": {
			"auth": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx="
		}
	},
	"HttpHeaders": {
		"User-Agent": "Docker-Client/19.03.14 (linux)"
	}
}

```

### イメージに自分のタグを付ける(docker image tag IMAGE:TAG USER/IMAGE:TAG)

手元にあるイメージを確認して、登録したいイメージに自分のログイン名の付いたタグを付ける。

```
$ docker image ls postgres13
REPOSITORY                         TAG                 IMAGE ID            CREATED             SIZE
postgres13                         alpine              a2bce220cd77        4 hours ago         160MB
postgres13                         debian              94952dd1306a        4 hours ago         314MB

$ docker image tag postgres13:alpine my-login-name/postgres13:alpine

```
ここで、(USER/IMAGE:TAG) は、(DockerHubのログイン名/イメージ名:タグ名) 


タグを追加後、イメージの詳細情報を見ると、RepoTags に指定した自分ログイン名を含むタグが追加されていることがわかる。

```
$ docker image inspect postgres13:alpine | head
[
    {
        "Id": "sha256:a2bce220cd7787b2e68e745ddba601e6ce70e868e421731963946f80b7be0e61",
        "RepoTags": [
            "my-login-name/postgres13:alpine",
            "postgres13:alpine"
        ],
        "RepoDigests": [
            "my-login-name/postgres13@sha256:7b44cf0d0e52c8cb2303d14886be4bae17413bd093572d792adef827b345bc1d"
        ],
...
```

