---
title: 加密 DNS
date: 2018-10-01 15:05:21
tags: [dnscrypt, Linux, Windows]
discription: 安装 dnscrypt-proxy 加密 DNS 请求
---

# 加密 DNS

[dnscrypt-proxy][1] 是一款灵活的 、拥有加密协议的 DNS 代理软件，可以实现 DNS 流量的加密与验证。

## 在 Linux 加密 DNS

下载 [Linux 64位安装包][2]，解压到指定目录

```shell
wget https://github.com/jedisct1/dnscrypt-proxy/releases/download/2.0.16/dnscrypt-proxy-linux_x86_64-2.0.16.tar.gz
tar zxf dnscrypt-proxy-linux_x86_64-2.0.16.tar.gz -C <your-services-dir>
mv linux_x86_64 dnscrypt_proxy
```

配置`dnscrypt-proxy.toml`

```toml
ipv4_servers = true
ipv6_servers = false
require_dnssec = true
require_nolog = true
require_nofilter = true
cache = true
block_ipv6 = true
force_tcp = false
listen_addresses = ["127.0.0.1:53", "[::1]:53"]
max_clients = 250
dnscrypt_servers = true
doh_servers = true
daemonize = false
timeout = 2500
keepalive = 30
netprobe_timeout = 45
log_level = 0
use_syslog = false
cert_refresh_delay = 240
fallback_resolver = "114.114.114.114:53"
ignore_system_dns = false
log_files_max_size = 10
log_files_max_age = 7
log_files_max_backups = 1
cache_size = 256
cache_min_ttl = 600
cache_max_ttl = 86400
cache_neg_ttl = 60

[query_log]
format = "ltsv"
file = "query.log"

[nx_log]
format = "ltsv"

[blacklist]
blacklist_file = "blacklist.txt"
log_file = "blocked.log"
log_format = "ltsv"

[ip_blacklist]

[sources]

[sources.public-resolvers]
urls = ["https://raw.githubusercontent.com/DNSCrypt/dnscrypt-resolvers/master/v2/public-resolvers.md", "https://download.dnscrypt.info/resolvers-list/v2/public-resolvers.md"]
minisign_key = "RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3"
cache_file = "public-resolvers.md"
refresh_delay = 72
prefix = ""
```

启动

```shell
./dnscrypt-proxy -service install
./dnscrypt-proxy -service start
```

修改`resolv.conf`配置

## 在 Winwos 上加密 DNS

下载 [Simple DNSCrypt][3] 安装即可。

[1]: https://github.com/jedisct1/dnscrypt-proxy
[2]: https://github.com/jedisct1/dnscrypt-proxy/releases/download/2.0.16/dnscrypt-proxy-linux_x86_64-2.0.16.tar.gz "2.0.16 版本包"
[3]: https://simplednscrypt.org/ "Simple DNSCrypt 官网"