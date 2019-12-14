---
title: CentOS 7 上编译 OpenSSL 与 Nginx
date: 2019-11-05 11:46:33
tags: [Nginx, OpenSSL, CentOS]
description: Compile latest version of Nginx and OpenSSL on CentOS 7
---


##  Download source code

```shell
# install tool
sudo yum update -y
sudo yum install -y vim curl wget tree

# openssl
git clone https://github.com/openssl/openssl.git

# nginx
wget https://nginx.org/download/nginx-1.17.5.tar.gz && sudo tar zxvf nginx-1.17.5.tar.gz

# PCRE version 8.42
wget https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz && sudo tar xzvf pcre-8.43.tar.gz

# zlib version 1.2.11
wget https://www.zlib.net/zlib-1.2.11.tar.gz && sudo tar xzvf zlib-1.2.11.tar.gz

# rtmp module(optional)
git clone git://github.com/arut/nginx-rtmp-module.git
```

## Compile Install

```shell
# tool
sudo yum install -y make gcc perl pcre-devel zlib-devel
sudo yum install perl-core zlib-devel -y
sudo yum group install 'Development Tools' -y

# openssl
cd openssl
git branch -av
git tag -l
git checkout -b OpenSSL_1_1_1d OpenSSL_1_1_1d

./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib

# zlib = enable the compression using zlib library.

# compile openssl
make
make test
make install

vim /etc/ld.so.conf.d/openssl.conf

/usr/local/ssl/lib

ldconfig -v
ldconfig -v | grep ssl
mv /bin/openssl /bin/openssl.BEKUP
vim /etc/profile.d/openssl.sh

# Set OPENSSL_PATH
OPENSSL_PATH="/usr/local/ssl/bin"
export OPENSSL_PATH
PATH=$PATH:$OPENSSL_PATH
export PATH

chmod +x /etc/profile.d/openssl.sh
source /etc/profile.d/openssl.sh
which openssl
openssl version -a
```

```shell
# nginx dependency
yum install -y perl perl-devel perl-ExtUtils-Embed libxslt libxslt-devel libxml2 libxml2-devel gd gd-devel GeoIP GeoIP-devel

sudo cp ~/nginx-1.17.5/man/nginx.8 /usr/share/man/man8
sudo gzip /usr/share/man/man8/nginx.8
ls /usr/share/man/man8/ | grep nginx.8.gz
# Check that Man page for Nginx is working:
man nginx


# compile nginx
./configure --prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib64/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--user=nginx \
--group=nginx \
--build=CentOS \
--builddir=nginx-1.17.5 \
--with-select_module \
--with-poll_module \
--with-threads \
--with-file-aio \
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_xslt_module=dynamic \
--with-http_image_filter_module=dynamic \
--with-http_geoip_module=dynamic \
--with-http_sub_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_mp4_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_auth_request_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_slice_module \
--with-http_stub_status_module \
--with-http_perl_module=dynamic \
--with-perl_modules_path=/usr/lib64/perl5 \
--with-perl=/usr/bin/perl \
--http-log-path=/var/log/nginx/access.log \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--with-mail=dynamic \
--with-mail_ssl_module \
--with-stream=dynamic \
--with-stream_ssl_module \
--with-stream_realip_module \
--with-stream_geoip_module=dynamic \
--with-stream_ssl_preread_module \
--with-compat \
--with-pcre=../pcre-8.43 \
--with-pcre-jit \
--with-zlib=../zlib-1.2.11 \
--with-openssl=../openssl \
--with-openssl-opt=no-nextprotoneg \
--with-debug \
--add-module=../nginx-rtmp-module


sudo make
sudo make install

sudo ln -s /usr/lib/nginx/modules /etc/nginx/modules


# check
sudo nginx -t

sudo mkdir -p /var/cache/nginx/client_temp
sudo chmod 700 /var/cache/nginx/client_temp
sudo chown nginx:root /var/cache/nginx/client_temp
sudo useradd --system --home /var/cache/nginx --shell /sbin/nologin --comment "nginx user" --user-group nginx

nginx -V
```

add nginx service `sudo vim /etc/systemd/system/nginx.service`

```ini
[Unit]
Description=nginx - high performance web server
Documentation=https://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
```

## Reference

- https://www.howtoforge.com/tutorial/how-to-install-openssl-from-source-on-linux/
- https://www.howtoforge.com/how-to-build-nginx-from-source-on-centos-7/