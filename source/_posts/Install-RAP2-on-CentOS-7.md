---
title: CentOS 7 部署 RAP2
date: 2019-01-04 01:21:30
tags: [Rap2, Mock]
description: CentOS 7 部署 RAP2
---

## 环境

- Node.js 安装
- MySQL 安装
- Redis 安装

## 配置

1. 创建数据库

```shell
mysql -e 'CREATE DATABASE IF NOT EXISTS RAP2_DELOS_APP DEFAULT CHARSET utf8 COLLATE utf8_general_ci';
```

2. RAP2 配置

   1. `rap2-dolore/src/config/config.prod.ts`

      ```ts
      # 修改域名
      module.exports = {
         serve: 'http://rap2api.taobao.org',
         keys: ['some secret hurr'],
         session: {
           key: 'koa:sess'
         }
      }
      ```

   2. `rap2-delos/src/config/config.prod.ts`

      ```ts
      #端口配置
      let config: IConfigOptions =  {
           version: '2.3',
           serve: {
              port: (process.env.EXPOSE_PORT && parseInt(process.env.EXPOSE_PORT)) || 8088,
           },

      #数据库配置
          let config: IConfigOptions =  {
          dialect: 'mysql',
          host: process.env.MYSQL_URL || 'localhost',
          port: (process.env.MYSQL_PORT && parseInt(process.env.MYSQL_PORT)) || 3306,
          username: process.env.MYSQL_USERNAME || 'root',
          password: process.env.MYSQL_PASSWD || '',
          database: process.env.MYSQL_SCHEMA || 'rap',
      ```

3. 启动

   1. rap2-dolores 启动

      进入代码目录

        ```shell
        pm2 start -n rap2-dolores npm -- run dev
        ```

   2. rap2-delos 启动

      进入代码目录

       ```shell
       # pm2 start -n rap2-delos npm -- start
       npm start
       ```

   3. redis-server 启动

      ```shell
      pm2 start redis-server --name redis-server
      ```

4. Nginx 反向代理

   ```conf
     location / {
       client_max_body_size 0;
       #gzip off;
       gzip on;
       gzip_min_length 1k;
       gzip_comp_level 6;#6
       gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;
       gzip_vary on;
       gzip_buffers 32 4k;
       ## https://github.com/gitlabhq/gitlabhq/issues/694
       ## Some requests take more than 30 seconds.
       proxy_read_timeout      300;
       proxy_connect_timeout   300;
       proxy_redirect          off;

       #proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection $connection_upgrade;

       #proxy_set_header    Host                $http_host;
       #proxy_set_header    X-Real-IP           $remote_addr;
       #proxy_set_header    X-Forwarded-Ssl     on;
       proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
       proxy_set_header    X-Forwarded-Proto   $scheme;
      proxy_pass http://127.0.0.1:3000;
     }
   ```

## 参考

- https://github.com/thx/rap2-delos/issues/119
- https://github.com/thx/rap2-delos
- https://github.com/jawil/blog/issues/7