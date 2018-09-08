---
title: Hexo 安装教程
tags: [Hexo, 安装教程]
description : 本文是一篇简单的Hexo的安装教程
---

# 依赖软件

* [Git](https://git-scm.com/)
* [Node.js](https://nodejs.org)

# Git 安装

* Windows: 下载并安装 [git](https://git-scm.com/download/win)。
* Mac: 使用 [Homebrew](http://mxcl.github.com/homebrew/), [MacPorts](http://www.macports.org/) or [installer](http://sourceforge.net/projects/git-osx-installer/) 安装。
* Linux (Ubuntu, Debian): `sudo apt-get install git-core`
* Linux (Fedora, Red Hat, CentOS): `sudo yum install git-core`

## Git 密钥生成

```shell
ssh-keygen -t rsa -C "your-email-address"
```

# Node.js 安装

* Windows: 下载最新 LTS 包安装即可，或使用 [nvm-window](https://github.com/coreybutler/nvm-windows) 安装。
* Linux: 参考官网使用 PackageManager [安装](https://nodejs.org/en/download/package-manager/)，或使用 [Node Version Manager](https://github.com/creationix/nvm) 安装。

# Hexo 安装

启动系统命令行终端，使用 npm 命令安装 Hexo

```shell
npm install -g hexo-cli
```

# 插件安装

1. hexo-renderer-markdown-it-plus

Hexo 的默认渲染插件是 hexo-renderer-marked，缺少很多功能，比如不支持 GFM、上下标、emoji 等，使用 `hexo-renderer-markdown-it` 加强版补足。

```shell
npm un hexo-renderer-marked --save
npm i hexo-renderer-markdown-it-plus --save
```

2. hexo-math

```shell
npm install hexo-math --save
```

# 参考

* [Hexo 官方安装教程][1]

[1]: https://hexo.io/docs/index.html    "Hexo 官方安装教程"
