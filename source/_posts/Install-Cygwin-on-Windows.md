---
title: Cygwin 安装教程
tags: [Cygwin, Install]
description: Cygwin 简易安装教程
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