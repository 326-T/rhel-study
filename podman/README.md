# コンテナ

## podman

対応表
|docker|podman|
|---|---|
|docker login docker.io|podman login docker.io|
|docker build|podman build|
|docker run|podman run|
|docker images|podman images|
|docker ps|podman ps|
|docker inspect|podman inspect|
|docker pull|podman pull|
|docker cp|podman cp|
|docker exec|podman exec|
|docker rm|podman rm|
|docker rmi|podman rmi|
|docker search|podman search|

podman のインストール

```bash
dnf install container-tools
```

## ネットワークの作成

```bash
# 作成
podman network create --subnet 10.89.1.0/24 --gateway 10.89.1.1 frontend
#　アタッチ
podman run -d --name db_01 --network frontend registry.lab.example.com/rhel8/mariadb-105
## もしくは
podman run -d --name db_01 registry.lab.example.com/rhel8/mariadb-105
podman network connect frontend db_01
```


## コンテナのデーモン化
```shell
cd ~/.config/systemd/user
podman generate systemd --name webapp --files --now
podman stop webapp
podman rm webapp
systemctl --user daemon-reload
systemctl enable --now --user container-webapp.service
```