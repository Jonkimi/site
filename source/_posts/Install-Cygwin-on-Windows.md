---
title: Cygwin 安装教程
date: 2018-09-10 18:27:40
tags: [Cygwin, 安装]
description: 本文介绍 Cygwin 的安装、镜像配置以及 Cygwin 包管理器 apt-cyg 的安装
---

## Website

https://cygwin.com/install.html

## Install

1. Download

   https://cygwin.com/setup-x86_64.exe

2. Mirror

   http://mirrors.163.com/cygwin/

3. Pre Install package

   gcc/g++,make,gdb,wget,tar,gawk,bzip2

4. apt-cyg

   ```shell
   wget https://raw.githubusercontent.com/transcode-open/apt-cyg/master/apt-cyg -P /bin
   chmod +x apt-cyg
   ```