---
title: Install Harbor Docker Image Registry
tags: [Docker, Harbor]
description: Install Harbor Docker Image Registry
date: 2020-01-04 12:46:05
---

## Requirements

- Docker
- Docker-Compose

```shell
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo groupadd docker
sudo usermod -aG docker $USER
```

## Install

1. download installer package

```shell
wget https://github.com/goharbor/harbor/releases/download/v1.9.4/harbor-offline-installer-v1.9.4.tgz
tar zxf harbor-offline-installer-v1.9.4.tgz
```

2. yaml configuration

```yaml
# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 8000
external_url: https://harbor.example.com
harbor_admin_password: 123456
```

3. Install and configure

```shell
./install.sh
docker-compose stop
```

registry config.yml `common/config/registry/config.yml`

```yml
http:
  relativeurls: true
```

nginx nginx.conf `common/config/nginx/nginx.conf`

```conf
# When setting up Harbor behind other proxy, such as an Nginx instance, remove the below line if the proxy already has similar settings.
# proxy_set_header X-Forwarded-Proto $scheme;
```


4. nginx configuration

```conf
upstream backend {
  server 127.0.0.1:8000;
}

server {
    listen 0.0.0.0:443 ssl http2;
    server_name example.com;
    server_tokens off;
    access_log  /var/log/nginx/harbor_access.log;
    error_log   /var/log/nginx/harbor_error.log;

    location / {
        client_max_body_size 0;
        gzip off;
        proxy_read_timeout      300;
        proxy_connect_timeout   300;
        proxy_redirect          off;

        proxy_http_version 1.1;

        proxy_set_header    Host                $http_host;
        proxy_set_header    X-Real-IP           $remote_addr;
        proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto   $scheme;

        proxy_pass http://backend;
    }
}
```

## Usage

```shell
docker login [server]
docker logout [server]
docker tag SOURCE_IMAGE[:TAG] example.com/project/IMAGE[:TAG]
docker push example.com/project/IMAGE[:TAG]
```


## Reference

- https://docs.docker.com/install/linux/docker-ce/ubuntu/
- https://docs.docker.com/compose/install
- https://github.com/goharbor/harbor/blob/master/docs/installation_guide.md
- https://github.com/docker/distribution/issues/970