---
title: 申请Letsencrypt免费HTTPS证书
date: 2018-07-28 13:57:23
tags: [Letsencrypt, 免费HTTPS证书, acme]
description: 使用acme给阿里云(aliyun)或namesilo网站申请免费HTTPS证书
---

## 安装acme

```shell
apt-get install socat  
curl  https://get.acme.sh | sh
```

## 申请证书

1. 阿里云申请Letsencrypt证书

    ```shell
    acme.sh --issue --dns dns_ali -d example.com -d *.example.com
    ```

2. Namesilo申请Letsencrypt证书

    ```shell
    export PROVIDER=namesilo
    export LEXICON_NAMESILO_TOKEN="namesilo-api-token"
    acme.sh --issue  -d test.acme.sh  --dns dns_lexicon  --dnssleep 960
    ./acme.sh --issue --dns dns_lexicon -d example.com -d *.example.com --dnssleep 960
    ```

## 问题

1. pyconfig.h: No such file or directory

    ```shell
    sudo apt-get install python-dev libxml2-dev libxslt-dev  libssl-dev  build-essential libffi-dev
    sudo pip install cryptography enum34
    ```

2. 测试HTTPS

    > openssl s_client -connect domain[:port]  

## 参考

https://github.com/Neilpang/acme.sh

https://github.com/Neilpang/acme.sh/wiki/How-to-use-lexicon-dns-api

https://www.tecmint.com/redirect-http-to-https-on-apache/