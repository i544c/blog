---
title: Docker Swarmを使い始めた
date: "2019-06-11"
layout: "post"
---

Dockerはローカルでの開発でよく使っている。ただデプロイとなると、Herokuを使ったり、dokkuを使ったり、はたまたコンテナ技術無しにサーバで直にアプリケーションを実行したりしている。

せっかくDockerにSwarmという便利な機能があるのだから、使おうと思った。

学ぶのには以下を参考にした。

[https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)

Dockerが何たるか、からDocker SwarmやDocker Deployまで学べるので、丁度良かった。Part3辺りから知らなかったので、以下に知見を記す。

まずはデプロイしたいサービスのDocker Imageと、docker-compose.ymlを書く。

Docker ImageはDockerHubだったり自前のレジストリサーバに上げておく。

docker-compose.ymlにはサービスのとこにdeployというキーを書き、そこにswarmで展開した際のコンテナの総数だったり、リソースの制限だったりを書く。

docker-compose.yml

```
...
    deploy:
      replicas: 5
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
```

Docker Swarm clusterをローカルで試すために、VirtualBoxを使用する。

```
pacman -S virtualbox virtualbox-host-dkms linux-headers
echo vboxdrv >> /etc/modules-load.d/vbox.conf
```

再起動すると、VirutalBoxが使えるようになる。

docker-machineを使って、tinylinux(docker入り)のVMを作成、管理できるようにする。

```
pacman -S docker-machine
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2
```

swarm clusterを構築する

```
docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1's IP addr>"
# swarm managerの設定ができて、他のマシンをnodeに追加するコマンドが表示される
docker-machine ssh myvm2 "docker swarm join --token <token> <ip>:2377"
```

ローカルに環境変数を食わせて、dockerがmyvm1をイジれるようにする

```
eval $(docker-machine env myvm1)
docker stack deploy -c docker-compose.yml getstartedlab
```

これでVMのIP(どちらでも可)でブラウザからアクセスすると、自分が書いたアプリケーションがVMの中で動いているのを確認できる。感動！接続先のコンテナは接続する度に変わるが、その仕組み(順番だったりランダムだったり)は設定で変えられる。

### 運用

学内で運営しているRailsのサービス(サーバ2, 3台)を、Docker Swarmで管理する。たかだか2, 3台なのだが、現状は1台のみ、Apache+Passengerで動いていて、Infra as Codeになっていない→簡単に冗長な構成にできないので厳しい。

そこでDockerとDocker Swarmを導入することで、サクッとプロセス増やしたりできるようになるし、他のサーバやPaaSに移行したりできるワケ。もうDocker Imageはできたし、期を見てDocker Swarmやっていくぞ。
