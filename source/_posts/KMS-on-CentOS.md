---
title: CentOS搭建KMS服务
date: 2018-06-28 10:44:48
tags: [KMS, CentOS]
description: 在 CentOS 上基于`micropython`安装`py-kms`搭建KMS服务
---

## 依赖工具

* [micropython](https://github.com/micropython/micropython.git)
* [py-kms](https://github.com/ThunderEX/py-kms.git)

## mircropython安装

参考`README.md`里关于`The Unix version`的内容

1. 下载最新`tag`代码

```shell
git clone https://github.com/micropython/micropython.git
cd micropython
git tag
git checkout -b tag-1.9.3 v1.9.3
```

2. 安装额外依赖

```shell
yum groupinstall "Development tools"
yum install libffi-devel
```

3. 编译

```shell
git submodule update --init
cd ports/unix
make deplibs
make axtls
make
```

4. 安装

```shell
./micropython
ls /usr/sbin/mkfs -l
```

在`~/.bash_profile`中添加环境变量

```ini
MICROPYTHON_HOME=/root/kms/micropython/ports/unix
PATH=$PATH:$HOME/bin:$MICROPYTHON_HOME
```

## py-kms 安装

1. 安装

``` shell
cd py-kms/
micropython -m upip install -r requirements-micropython.txt
chomod +x *.sh
```

2. 启动

```shell
python server.py
```

3. 测试

```shell
python client.py 0.0.0.0 1688
```

## 问题定位

```shell
$ micropython -m upip install -r requirements-micropython.txt
Installing to: /root/.micropython/lib/
Warning: pypi.python.org SSL certificate is not validated
Error installing 'micropython-argparse': Package not found, packages may be partially installed
```

解决：更新到v1.9.4