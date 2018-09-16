---
title: 测试Reno,Cubic和BBR三种TCP阻塞控制协议的性能
date: 2018-06-28 11:06:48
tags: [TCP阻塞控制协议, BBR, Cubic, Reno]
description: 在CentOS 7 上使用flent测试Reno,Cubic和BBR三种TCP阻塞控制协议的性能
---

#### 测试环境

1. 系统

   服务器 Ubuntu 14.04，客户端CentOS 7

2. 网络

#### 测试工具

Flent 简介[^1],安装 说明[^2]

其中CentOS 7 安装 netperf 使用与服务器相同版本的源代码编译安装

#### 测试步骤

服务器启动`netserver`

```shell
netserver &
```

预设三种协议都已经完成安装

查看所有的阻塞协议

```shell
sysctl net.ipv4.tcp_available_congestion_control
```

查看当前的阻塞协议

```shell
sysctl net.ipv4.tcp_congestion_control
```

配置当前的阻塞协议`/etc/sysctl.conf`

```ini
net.ipv4.tcp_congestion_control=bbr
```

配置生效

```shell
sysctl -p
```

在客户机上使用flent测试，当前使用命令，也可使用`flent-gui`命令打开GUI界面操作

```shell
flent tcp_upload -p totals -l 90 -H 172.96.199.44 -t reno-server -o reno-server.png
```

每个命令执行后都会生成指定名字的数据包和统计图。

使用flent-gui可以将每次测试生成的数据包合入一张统计图。

#### 参考

[^1]: [Flent官网][1]
[^2]: [Flent安装教程][2]
[^3]: [安装BBR][3]

[1]: https://flent.org "Flent官网"
[2]: https://flent.org/intro.html#installing-flent "Flent安装教程"
[3]: https://www.cnblogs.com/luanlengli/p/8733660.html "安装BBR"
[4]: http://blog.cerowrt.org/post/bbrs_basic_beauty/ "A quick look at TCP BBR"
[5]: https://blog.csdn.net/dog250/article/details/52830576 "BBR 简单解析"