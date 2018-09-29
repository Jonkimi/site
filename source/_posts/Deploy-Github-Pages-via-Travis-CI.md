---
title: 使用 Travis CI 自动部署 Github Pages
date: 2018-07-27 12:38:56
tags: [Github Pages, 博客, Travis CI, Hexo]
description: 本文介绍如何使用 Travis CI 自动部署Hexo博客到 Github Pages
---

## Travis CI 介绍

**Travis CI** 是在[软件开发](https://zh.wikipedia.org/wiki/%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91)领域中的一个在线的，分布式的持续集成服务，用来构建及测试在[GitHub](https://zh.wikipedia.org/wiki/GitHub)托管的代码。

## 注册账户

**Travis CI** 可使用Github账户登录

## travis 配置

在博客项目根目录创建`.travis.yml`配置文件

```yaml
language: node_js

sudo: false

node_js:
  - "node"

cache:
  apt: true
  directories:
    - node_modules

env:
#  - CXX=g++-4.8

addons:
#  apt:
#    sources:
#      - ubuntu-toolchain-r-test
#    packages:
#      - g++-4.8
  ssh_known_hosts: github.com

install:
  - npm install

before_script:
 - git config --global user.name "Jonkimi"
 - git config --global user.email "<your-email>"
 - sed -i'' "s~git@github.com:Jonkimi/jonkimi.github.io.git~https://${GH_TOKEN}@github.com/Jonkimi/jonkimi.github.io.git~" _config.yml
 - git clone -b master https://github.com/Jonkimi/jonkimi.github.io.git .deploy_git # 此配置用来保留jonkimi.github.io项目的提交记录

script:
#  - npm run eslint
  - hexo g
  - hexo deploy --silent


branches:
  only:
    - hexo

deploy:
  provider: pages
  skip_cleanup: true
  keep-history: true
  local_dir: public
  github_token: $GH_TOKEN # Set in travis-ci.org dashboard
  on:
    branch: hexo
  repo: jonkimi/jonkimi.github.io
  target_branch: develop
  fqdn: jonkimi.com
```

## 开启持续集成

在travis-ci网站的`profile`页面添加需要持续集成的功能。

### 配置

在 Github 的 Personal access tokens 界面申请公开仓库权限 token

![Github personal access token](https://image.jonkimi.com/images/2018/09/18/github_personal_access_tokens_page_.png)

获取到 token 后到 travis-ci 项目配置界面添加环境变量`GH_TOKEN`，设置 Buid 出发条件。

![travis-ci 项目添加环境变量](https://image.jonkimi.com/images/2018/09/18/travis-ci-add-project-env.png)

至此完成配置，每次仓库提交到Github上时，都会出发持续集成，依靠`.travis.yml`中的脚本配置自动部署提交代码到Github Pages 仓库。

## 参考

* [Travis CI 登录与配置][3]
* [Travis CI 配置文件选项][2]
* [Travis CI 配置文件参考][4]

[1]: https://github.com/settings/tokens "Github Token管理界面"
[2]: https://docs.travis-ci.com/user/deployment/pages/ "Travis CI 部署 Github Pages 官方文档"
[3]: https://juejin.im/post/596e39916fb9a06baf2ed273 "Travis CI 配置文件"
[4]: https://github.com/hexojs/site/blob/master/.travis.yml "Hexo website travis.yml"
[5]: https://github.com/hexojs/eslint-config-hexo "添加 eslint"
[6]: https://takuti.me/note/travis-gh-pages-deployment/ "Travis CI 配置文件参考"