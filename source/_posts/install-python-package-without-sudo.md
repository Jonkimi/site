---
title: 非 sudoer 用户安装 python 包
date: 2018-09-15 18:27:40
tags: [Python Environment]
description: Linux环境下无 root 权限或非 sudoer 用户如何成功安装 python 包
---

## 安装

```shell
pip install --user <package-name>
```

## 生效

在`~/.bashrc`添加以下配置

```bash
export PATH=~/.local/bin:$PATH
export PYTHONPATH=$HOME/.local/lib/<python-version>/site-packages
```

`source ~/.bashrc`执行生效
