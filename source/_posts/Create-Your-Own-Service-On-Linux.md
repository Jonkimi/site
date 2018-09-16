---
title: Linux 创建自定义服务
date: 2018-09-16 23:32:00
tags: [Linux, 自定义服务, Systemd, init]
description: 本文简单介绍在Linux通过配置自定义服务
---
# Linux 创建自定义服务

## Systemd service[^1][^2][^3]

```ini
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
# Nginx will fail to start if /run/nginx.pid already exists but has the wrong
# SELinux context. This might happen when running `nginx -t` from the cmdline.
# https://bugzilla.redhat.com/show_bug.cgi?id=1268621
ExecStartPre=/usr/bin/rm -f /run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

[Unit]部分主要是对这个服务的说明，内容包括Description和After，Description用于描述服务，After用于描述服务类别。

[Service]部分是服务的关键，是服务的一些具体运行参数的设置：

1. Type=forking 是后台运行的形式；

2. PIDFile 为存放PID的文件路径；

3. ExecStartPre 为服务启动命令前执行的命令；

4. ExecStart 为服务的具体运行命令；

5. ExecReload 为重启命令；

6. ExecStop 为停止命令；

7. PrivateTmp=True 表示给服务分配独立的临时空间。

   ==注意：[Service]部分的启动、重启、停止命令全部要求使用绝对路径，使用相对路径则会报错！==[^4]

### 服务操作

启动服务

```shell
systemctl start <service-name>
```

停止服务

```shell
systemctl stop <service-name>
```

服务开机自动启动

```shell
systemctl enable <service-name>
```

服务取消开机启动

```shell
systemctl disable <service-name>
```

服务状态查看

```shell
systemctl status <service-name>
```

服务重启

```shell
 systemctl reload <service-name>
```

## Upstart Service

在`/etc/init.d/`目录下新建`service-name`文件[^5]

```bash
service_name="service_name"
service_script_path="service_script_path"

start()
{
    echo "$service_name service start"
    chmod a+x $service_script_path && nohup ./$service_script_path 
}

stop()
{
    echo "$service_name service stop"
    server_process_id=`ps -aux | grep $service_name | cut -d " " -f 6`
    echo "server_process_id="server_process_id
    kill -9 $server_process_id
}

case $1 in
start)
    start
    ;;
stop)
    stop
    ;;
restart)
    echo "resert the $service_name"
    stop
    start
    ;;
*)
    echo "undefine operation"
    ;;
esac
```

## 参考

[^1]: [Upstart 与 Systemd][1]
[^2]: [Systemd 替代 init][2]
[^3]: [Systemd 鸟哥私房菜教程][4]
[^4]: [Systemd 博客参考][5]
[^5]: [Udstart 自定义服务模板][3]



[1]: https://wiki.ubuntu.com/SystemdForUpstartUsers "Upstart 与 systemd 比较"
[2]: https://www.tecmint.com/systemd-replaces-init-in-linux/ "systemd 替代 init"
[3]: https://peter517.github.io/2015/08/10/Ubuntu%E4%B8%AD%E8%87%AA%E5%AE%9A%E4%B9%89%E6%9C%8D%E5%8A%A1/ "Ubuntu 自定义服务模板"
[4]: https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/150.html	" 鸟哥的 Linux 私房菜"
[5]: https://blog.csdn.net/yuanguozhengjust/article/details/38019923 "systemd快速配置理解"
