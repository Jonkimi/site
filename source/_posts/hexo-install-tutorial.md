---
title: Hexo 安装教程
tags: [Hexo, 安装教程]
description: 本文是一篇简单的Hexo的安装教程，包括安装`hexo-renderer-markdown-it-plus`渲染插件支持注脚与上下标，安装`hexo-math`插件支持`mathjax`显示数学公式
---

# 依赖软件

* [Git](https://git-scm.com/)
* [Node.js](https://nodejs.org)

# Git 安装[^1]

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

# 更换主题

项目根目录下执行下面的命令下载`hexo-theme-next`主题

```shell
git subtree add -P themes/hexo-theme-next  https://github.com/theme-next/hexo-theme-next.git  v6.4.1 --squash
```

`_config.yml`修改`theme`配置

```yml
...
theme: hexo-theme-next
...
```

# 插件安装

1. hexo-renderer-markdown-it-plus

Hexo 的默认渲染插件是 hexo-renderer-marked，缺少很多功能，比如不支持 GFM、上下标、注脚、emoji 等，使用 `hexo-renderer-markdown-it` 加强版补足。[^2]

```shell
npm un hexo-renderer-marked --save
npm i hexo-renderer-markdown-it-plus --save
```

2. hexo-math

安装hexo-math插件

```shell
npm install hexo-math --save
```

修改`hexo-theme-next`配置文件启动`mathjax`

在需要渲染公式界面添加配置`mathjax: true`

# 配置

1. 创建标签云页

先创建tag页面，然后在主题`_config.yml`的Menu中配置

```shell
hexo new page tags
```

2. 设置 DISQUS

注册`DISQUS`账户，创建网站[^4]，修改主题配置文件，启动`DISQUS`

```yml
...
# Disqus
disqus:
  enable: true
  shortname: your-shortname
  count: true
  lazyload: false
  ...
```

# 参考

[^1]: [Hexo 官方安装教程][1]
[^2]: [Hexo 修改渲染插件][2]
[^3]: [hexo-theme-next wiki][3]
[^4]: [DISQUS 创建网站][4]

[1]: https://hexo.io/docs/index.html    "Hexo 官方安装教程"
[2]: https://hmgqzx.github.io/wiki/Hexo/Hexo%20%E6%8F%92%E4%BB%B6/MarkDown%20%E6%B8%B2%E6%9F%93%E6%8F%92%E4%BB%B6/	"Hexo修改渲染插件"
[3]: https://github.com/iissnan/hexo-theme-next/wiki
[4]: https://disqus.com/admin/create/