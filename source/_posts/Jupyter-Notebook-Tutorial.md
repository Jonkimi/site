---
title: Jupyter-Notebook-Tutorial
date: 2018-09-08 11:21:52
tags: [Jupyter, Jupyter-Notebook]
description: Jupyter的安装与使用
---

## 简介

> The Jupyter Notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and narrative text. Uses include: data cleaning and transformation, numerical simulation, statistical modeling, data visualization, machine learning, and much more.^[1]^

Jupyter Notebook 可以很方便的共享自己的成果。

## 安装

```shell
pip install jupyter
```

## 启动

进入 notebook 目录，执行以下脚本（可选：https，root权限）

```bash
#! /bin/bash
jupyter notebook  --certfile /etc/nginx/ssl/example.com.cer --keyfile /etc/nginx/ssl/example.com.key --allow-root > note.log 2>&1 &
```

## 停止

```shell
jupyter notebook stop
```

或者

```bash
#! /bin/bash
pid=`ps -ef | grep 'jupyter-notebook' | grep -v 'grep' | awk '{print $2}'`
if [[ -n $pid ]]; then
    kill -9 $pid
fi
```

## 修改密码

```shell
jupyter notebook password
```

## 参考

* [Jupyter 官网][1]

[1]: https://jupyter.org/   "Jupyter 官网"
