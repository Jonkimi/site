---
title: Chevereto 图床搭建教程
date: 2018-07-08 13:29:48
tags: [图床, Chevereto]
description: 使用Chevereto搭建HTTPS图床
---

## Chevereto 简介

[Chevereto](https://chevereto.com) 是一款图床服务器软件，其[社区版本](https://chevereto.com/free)可以让我们免费搭建自己的图床服务器。

## Chevereto 安装

1. 依赖

    - 云服务器 with LAMP
    - 域名

2. 安装包

    在 [Free Edition](https://chevereto.com/free) 页面下载最新版本安装包，下载地址：https://github.com/Chevereto/Chevereto-Free/releases/latest

3. 配置

    apache配置文件
    ```ini
    <VirtualHost *:80>
        ServerAdmin admin@images.example.com
        DocumentRoot /var/www/html/chevereto/
        ServerName images.example.com
        <Directory /var/www/html/chevereto/>
        Options FollowSymLinks
        DirectoryIndex index.php
        AllowOverride All
        Order allow,deny
        allow from all
        </Directory>
        ErrorLog /var/log/apache2/chevereto-error_log
        CustomLog /var/log/apache2/chevereto-access_log common
    </VirtualHost>
    ```
    https

    ```shell
    a2enmod ssl
    a2ensite default-ssl
    ```
    htaccess

    ```ini
    # Disable server signature
    ServerSignature Off
    
    # Disable directory listing (-indexes), Multiviews (-MultiViews) and enable Follow system links (+FollowSymLinks)
    Options -Indexes
    Options -MultiViews
    Options +FollowSymLinks
    php_value upload_max_filesize 90M
    <IfModule mod_rewrite.c>
    
        RewriteEngine On
        # If you have problems with the rewrite rules remove the "#" from the following RewriteBase line
        # You will also have to change the path to reflect the path to your Chevereto installation
        # If you are using alias is most likely that you will need this.
        RewriteBase /
    
        # 404 images
        # If you want to have your own fancy "image not found" image remove the "#" from RewriteCond and RewriteRule lines
        # Make sure to apply the correct paths to reflect your current installation
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteRule images/.+\.(gif|jpe?g|png|bmp) - [NC,L,R=404]
        #RewriteRule images/.+\.(gif|jpe?g|png|bmp) content/images/system/default/404.gif [NC,L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_URI} !\.(css|js|html|htm|rtf|rtx|svg|svgz|txt|xsd|xsl|xml|asf|asx|wax|wmv|wmx|avi|bmp|class|divx|doc|docx|exe|gif|gz|gzip|ico|jpe?g|jpe|mdb|mid|midi|mov|qt|mp3|m4a|mp4|m4v|mpeg|mpg|mpe|mpp|odb|odc|odf|odg|odp|ods|odt|ogg|pdf|png|pot|pps|ppt|pptx|ra|ram|swf|tar|tif|tiff|wav|wma|wri|xla|xls|xlsx|xlt|xlw|zip)$ [NC]
        RewriteRule . index.php [L]
        RewriteCond %{HTTPS} off
        RewriteRule (.*) https://%{SERVER_NAME}/$1 [R,L]
    </IfModule>
    ```

4. 修改文件上传大小上限

    /etc/nginx/nginx.conf

    ```ini
    ...
    http
    {
        ...
        client_max_body_size 40m;
        ...
    }
    ...
    ```



    /etc/php.ini

    ```ini
    memory_limit = 32M
    upload_max_filesize = 24M
    post_max_size = 32M
    ```

## 安装问题

1. cURL isn't installed and allow_url_fopen is disabled. Chevereto needs one of these to perform HTTP requests to remote servers.

    ```shell
    apt-get install php5-curl
    service apache2 restart
    ```

2. GD Library is not enabled in this server. GD is needed to perform image handling.

    ```shell
    apt-get install php5-gd
    service apache2 restart
    ```

3. Apache mod_rewrite is not enabled in this server. This must be enabled to run Chevereto.

    ```shell
    sudo a2enmod rewrite
    service apache2 restart
    ```

4. The requested URL /install was not found on this server

5. sysctl: cannot stat /proc/sys/status: No such file or directory

6. G\: Sessions are not working on this server due to missing write permission on session save path (php.ini session.save_path).

    实际session目录配置为`/etc/php-fpm.d/www.conf`下的
    ```ini
    php_value[session.save_path] = /var/lib/php/session
    ```
    修改该目录权限为同配置下的`user`与`group`


## 参考

[^1]: https://www.digitalocean.com/community/tutorials/how-to-set-up-mod_rewrite-for-apache-on-ubuntu-14-04
[^2]: https://www.digitalocean.com/community/questions/installing-the-gd-image-library
[^3]: https://hub.docker.com/r/furiousgeorge/chevereto/
[^4]: https://www.howtoforge.com/tutorial/ubuntu-chevereto-image-hosting/
[^5]: https://www.tecmint.com/redirect-http-to-https-on-apache/