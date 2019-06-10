---
title: Install-Gitlab-Runner-with-Docker
date: 2019-05-09 11:01:28
tags: [Gitlab, Runner, CI, Docker, Cache Server]
description: 安装 Gitlab-Runner，配置 Docker 与 Cache Server
---

## Gitlab-Runner 安装

安装命令

```shell
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
```

注册Runner

```shell
sudo gitlab-runner register
```

配置

```conf
[[runners]]
  name = "unity-compiler"
  url = "https://gitlab.example.com/"
  token = ""
  executor = "docker"
  [runners.docker]
    host= "tcp://localhost:2375"
    pull_policy = "if-not-present"
    extra_hosts = ["gitlab.example.com:192.168.3.55"]
    tls_verify = false
    image = "gableroux/unity3d:2017.4.10f1"
    privileged = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
  [runners.cache]
```

## Docker 安装

```shell
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
sudo apt-cache policy docker-ce
sudo apt install docker-ce -y
```

设置`DOCKER_HOST`

```shell
echo "export DOCKER_HOST=localhost:2375" >> ~/.bash_profile
```

镜像测试命令

```shell
docker run --net host -it --rm -e "UNITY_USERNAME=your-user-name" -e "UNITY_PASSWORD=your-password" gableroux/unity3d:2018.3.12f1
```

## Cache Server

```shell
 docker run -it --restart always -p 9005:9000 \
         -v /disk_dump/cache_server/.minio:/root/.minio:z -v /disk_dump/cache_server/export:/export:z \
         --name minio \
         minio/minio:latest server /export
```

> Can't mount host data directory to minio server docker container

[SELinux 添加权限][3]

```bash
cat /var/log/audit/audit.log | grep minio | grep denied | audit2allow -M minio
semodule -i minio.pp
```

[1]: https://gitlab.com/gitlab-org/gitlab-runner/blob/master/docs/install/linux-repository.md
[1]: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
[2]: https://docs.gitlab.com/runner/configuration/autoscale.html#distributed-runners-caching
[3]: https://github.com/minio/minio/issues/6237
