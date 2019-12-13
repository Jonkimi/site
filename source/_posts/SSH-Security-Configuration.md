---
title: SSH 安全
date: 2018-12-18 23:30:39
tags: [SSH, Security, 安全]
description: 提高 SSH 安全性
---

## SSH 安全

修改 SSH 服务配置文件`/etc/ssh/sshd_config`提高SSH安全性[^1]

## 关闭密码登录

修改`PasswordAuthentication`

```conf
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication no
```

复制公开密钥到远程服务器[^2]

```shell
ssh-copy-id -i /path/to/public-key-file user@host
```

## 使用安全算法

添加算法指定

```conf
Ciphers aes128-ctr,aes192-ctr,aes256-ctr

HostKeyAlgorithms ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-rsa,ssh-dss

KexAlgorithms ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256

MACs hmac-sha2-256,hmac-sha2-512
```

## 使用安全性更好的密钥生成算法

使用`ed25519`或者`RSA 4096`[^2]

```shell
ssh-keygen -t key_type -b bits -C "comment"
```

## 禁止 Root 远程登录

修改`PermitRootLogin`

```conf
PermitRootLogin No
```

## 更换端口

修改`Port`

```conf
Port 1122
```

## 参考

[^1]: https://www.ssh.com/ssh/sshd_config/
[^2]: https://www.cyberciti.biz/tips/linux-unix-bsd-openssh-server-best-practices.html
