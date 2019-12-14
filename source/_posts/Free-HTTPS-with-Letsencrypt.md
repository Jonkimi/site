---
title: 申请Letsencrypt免费HTTPS证书
date: 2018-07-28 13:57:23
tags: [Letsencrypt, 免费HTTPS证书, acme]
description: 使用开源工具 acme 给阿里云(aliyun)或 namesilo 或 cloudflare 的域名自动申请免费Letsencrypt 证书
---

## 安装acme

```shell
apt-get install socat  
curl  https://get.acme.sh | sh
```

## 申请证书

1. 阿里云申请Letsencrypt证书

    ```shell
    export Ali_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
    export Ali_Secret="jlsdflanljkljlfdsaklkjflsa"
    acme.sh --issue --dns dns_ali -d example.com -d *.example.com
    ```

2. Namesilo申请Letsencrypt证书

    ```shell
    export PROVIDER=namesilo
    export LEXICON_NAMESILO_TOKEN="namesilo-api-token"
    acme.sh --issue --dns dns_lexicon   -d test.acme.sh   --dnssleep 960
    ./acme.sh --issue --dns dns_lexicon -d example.com -d *.example.com --dnssleep 960
    ```
3. cloudflare

    ```shell
    # PROVIDER=cloudflare
    # SAVED_CF_Key= 
    # SAVED_CF_Email=
    acme.sh --issue --dns dns_cf  -d example.com -d *.example.com
    ```

3. 自动申请

    添加周期任务
    ```shell
    $ crontab -e
    # 添加已下配置
    21 0 * * * "/root/.acme.sh"/acme.sh  --cron --home "/root/.acme.sh" > /root/.acme.sh/run.log
    ```
## CAA Record

域名解析添加 CAA 记录 
```
0 issuewild "letsencrypt.org"
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

- https://github.com/Neilpang/acme.sh
- https://github.com/Neilpang/acme.sh/wiki/How-to-use-lexicon-dns-api
- https://www.tecmint.com/redirect-http-to-https-on-apache/