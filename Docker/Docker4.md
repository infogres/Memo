# Docker 入門(4)

## Docker ネットワーク

## Docker チュートリアル

- [GitHub - 7nohe/docker-lessons: Docker研修用資料](https://github.com/7nohe/docker-lessons)


- [Docker network 概論 - Qiita](https://qiita.com/TsutomuNakamura/items/ed046ee21caca4a2ffd9)
- [マルチホストでのDocker Container間通信 第1回: Dockerネットワークの基礎 - Uzabase Tech Blog](https://tech.uzabase.com/entry/2017/08/07/172411)

![cbd4f385.png](storage/977b6f5a-20cb-41e4-aa35-5d5315ff9dd1/cbd4f385.png)


Docker サービスを起動した時点で、 docker0 というインターフェースが作成される。

```
$ ifconfig docker0
...
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:fdff:fe72:45b0  prefixlen 64  scopeid 0x20<link>
        ether 02:42:fd:72:45:b0  txqueuelen 0  (\u30a4\u30fc\u30b5\u30cd\u30c3\u30c8)
        RX packets 316271  bytes 17720303 (17.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 563662  bytes 855000398 (855.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
...
```

### Dockerデフォルトネットワークの確認(docker network ls)

また、Dockerサービスを起動したときに、Dockerネットワークのデフォルト設定もされる。

```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
7e059b38ffc9        bridge              bridge              local
8344ef363aa6        host                host                local
65e6b3c0a2b0        none                null                local

```


### Deockerネットワークの生成(docker network create NETWORK)

コンテナ間の通信を行うためには、Docker内のネットワークを生成しておく。

```
$ docker network create busybox-network
f3e84504e9f771bb19ba3062c6c05e88d76e2a2b424ae180fefffa0d25a56613

$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
4e551f36dbea        bridge              bridge              local
f3e84504e9f7        busybox-network     bridge              local
5d9d4ab75d99        host                host                local
95fccd5e444d        none                null                local

```

### Deockerコンテナの起動(docker run --network NETWORK)

そして、コンテナ生成時に試用するそのネットワークを指定する。
ここでは、busybox をDockerコンテナで実行してみる。
busybox を起動するとプロンプト(/ #)が表示されるので、基本的なネットワークコマンドで設定状態を確認してみる(hostname, ifconfig, brctl show, route)。

まず、busybox1 という名前で1つ目のコンテナを起動する。

```
$ docker run --name busybox1 --network busybox-network -it --rm busybox
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
ea97eb0eb3ec: Pull complete 
Digest: sha256:bde48e1751173b709090c2539fdf12d6ba64e88ec7a4301591227ce925f3c678
Status: Downloaded newer image for busybox:latest
/ # hostname
9cd9707d9577

/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:1B:00:02  
          inet addr:172.27.0.2  Bcast:172.27.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:55 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:7046 (6.8 KiB)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # brctl show

/ # route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         172.27.0.1      0.0.0.0         UG    0      0        0 eth0
172.27.0.0      *               255.255.0.0     U     0      0        0 eth0

/ # ping busybox2
PING busybox2 (172.27.0.3): 56 data bytes
64 bytes from 172.27.0.3: seq=0 ttl=64 time=0.146 ms
64 bytes from 172.27.0.3: seq=1 ttl=64 time=0.129 ms
64 bytes from 172.27.0.3: seq=2 ttl=64 time=0.132 ms
^C
--- busybox2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.129/0.135/0.146 ms
/ # 
^P^Q

```
ここで、

    --network busybox-network: ネットワーク名を指定


次に、別の端末を開き、busybox2 という名前で2つ目のコンテナを起動する。

```
$ docker run --name busybox2 --network busybox-network -it --rm busybox
/ # hostname
8f5d2194c479

/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:1B:00:03  
          inet addr:172.27.0.3  Bcast:172.27.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:14 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:1762 (1.7 KiB)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # brctl show

/ # route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         172.27.0.1      0.0.0.0         UG    0      0        0 eth0
172.27.0.0      *               255.255.0.0     U     0      0        0 eth0

/ # ping busybox1
PING busybox1 (172.27.0.2): 56 data bytes
64 bytes from 172.27.0.2: seq=0 ttl=64 time=0.163 ms
64 bytes from 172.27.0.2: seq=1 ttl=64 time=0.130 ms
64 bytes from 172.27.0.2: seq=2 ttl=64 time=0.129 ms
^C
--- busybox1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.129/0.140/0.163 ms
/ # 

```

ここで、(Ctrl+P,Ctrl+Qで)コンテナを抜けてホスト上のネットワーク構成を見ると、br-f3e84504e9f7 と veth* という仮想インターフェースが作成されて ブリッジ接続されていることがわかる。

```
$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp3s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DEFAULT group default qlen 1000
    link/ether 80:ee:73:e1:14:3c brd ff:ff:ff:ff:ff:ff
3: enp0s31f6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 80:ee:73:e1:14:3b brd ff:ff:ff:ff:ff:ff
8: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default 
    link/ether 02:42:fd:72:45:b0 brd ff:ff:ff:ff:ff:ff
198: br-f3e84504e9f7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 02:42:58:9f:de:af brd ff:ff:ff:ff:ff:ff
200: veth8344899@if199: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-f3e84504e9f7 state UP mode DEFAULT group default 
    link/ether 22:62:84:7c:63:84 brd ff:ff:ff:ff:ff:ff link-netnsid 0
202: veth21f9806@if201: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-f3e84504e9f7 state UP mode DEFAULT group default 
    link/ether ca:ff:08:34:cd:72 brd ff:ff:ff:ff:ff:ff link-netnsid 1

$ brctl show
bridge name	bridge id		STP enabled	interfaces
br-f3e84504e9f7		8000.0242589fdeaf	no		veth21f9806
							                                                        veth8344899
docker0		8000.0242fd7245b0	no		

$ ifconfig
br-f3e84504e9f7: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.27.0.1  netmask 255.255.0.0  broadcast 172.27.255.255
        inet6 fe80::42:58ff:fe9f:deaf  prefixlen 64  scopeid 0x20<link>
        ether 02:42:58:9f:de:af  txqueuelen 0  (Ethernet)
        RX packets 40  bytes 2021 (2.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 81  bytes 8484 (8.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:fdff:fe72:45b0  prefixlen 64  scopeid 0x20<link>
        ether 02:42:fd:72:45:b0  txqueuelen 0  (Ethernet)
        RX packets 316271  bytes 17720303 (17.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 563662  bytes 855000398 (855.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

...
veth8344899: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::2062:84ff:fe7c:6384  prefixlen 64  scopeid 0x20<link>
        ether 22:62:84:7c:63:84  txqueuelen 0  (Ethernet)
        RX packets 28  bytes 2139 (2.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 111  bytes 11737 (11.7 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth21f9806: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::c8ff:8ff:fe34:cd72  prefixlen 64  scopeid 0x20<link>
        ether ca:ff:08:34:cd:72  txqueuelen 0  (Ethernet)
        RX packets 57  bytes 4012 (4.0 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 109  bytes 10142 (10.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```


### Deockerネットワークの詳細(docker network inspect NETWORK)

試用したDockerネットワークの詳細情報を見ると、起動した２つのコンテナそれぞれにインターフェースが定義されていることがわかる。

```
$ docker network inspect busybox-network
[
    {
        "Name": "busybox-network",
        "Id": "f3e84504e9f771bb19ba3062c6c05e88d76e2a2b424ae180fefffa0d25a56613",
        "Created": "2020-12-06T13:30:51.937449946+09:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.27.0.0/16",
                    "Gateway": "172.27.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "8f5d2194c4795e573a7bd04fbc3dc8a4107e1a158a164e5171472e647873e379": {
                "Name": "busybox2",
                "EndpointID": "fd236769d05568252438e6dce0713909285c1d7d3f6d61ff93b652d079c28867",
                "MacAddress": "02:42:ac:1b:00:03",
                "IPv4Address": "172.27.0.3/16",
                "IPv6Address": ""
            },
            "9cd9707d957786c362d82da4bcdce6bfd24f7d95942c1ae8489d09fb8d41b409": {
                "Name": "busybox1",
                "EndpointID": "a19a3a855e632b9d89f2706c95bd8b599119e92ca3dcf61e8bc3b596cd1d6aff",
                "MacAddress": "02:42:ac:1b:00:02",
                "IPv4Address": "172.27.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

```

なお、Dockerコンテナとイメージの情報は以下のとおり。

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
8f5d2194c479        busybox             "sh"                47 minutes ago      Up 47 minutes                           busybox2
9cd9707d9577        busybox             "sh"                49 minutes ago      Up 49 minutes                           busybox1

$ docker image ls busybox
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
busybox             latest              219ee5171f80        2 days ago          1.23MB

```


### DockerネットワークI/Fのポートフォワード(docker run -p ExPORT:PORT)

ポートフォワード機能(Port Forwarding)を使うとコンテナの外部（ホスト外部）からコンテナにアクセスできるようになる。
ポートフォワードは、コンテナ実行時に (-p) オプションで指定する。

例えば、ホストの8080番ポートにアクセスしたらコンテナの80番ポートにアクセスするようにするには、コンテナ実行時に "-p 8080:80" をオプションとして指定する。

```
$ docker container run --network=busybox-network -p 8080:80  \
                     -d --rm --name=busy-nginx  -v $(pwd):/usr/share/nginx/html:ro nginx
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
10053c586675        nginx               "/docker-entrypoint.\u2026"   6 minutes ago       Up 6 minutes        0.0.0.0:8080->80/tcp   busy-nginx
8f5d2194c479        busybox             "sh"                     3 hours ago         Up 3 hours                                 busybox2
9cd9707d9577        busybox             "sh"                     3 hours ago         Up 3 hours                                 busybox1

```

このように、nginx を起動すると、ホストまたはホスト外部からは http://(ホストのIPアドレス):8080/ でアクセスできる。

```
$ curl 192.168.1.37:8080
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <body>
    <h1>Hello World!</h1>
  </body>
</html>
```

また、busybox1 と busybox2 からはポート 80 でアクセスできる。

```
/ # nc busy-nginx 80
GET / HTTP/1.1

HTTP/1.1 400 Bad Request
Server: nginx/1.19.5
Date: Sun, 06 Dec 2020 08:02:40 GMT
Content-Type: text/html
Content-Length: 157
Connection: close

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>nginx/1.19.5</center>
</body>
</html>
```
※ ここでは改行コードにゴミが混じるため "Bad Request" となるが、サービスにアクセスできていることは確認できる。


