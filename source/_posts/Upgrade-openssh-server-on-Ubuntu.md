---
title: Upgrade openssh-server on Ubuntu
tags: [openssh]
description: Upgrade openssh-server on Ubuntu 16.04
date: 2020-04-17 16:35:05
---

## Backup

```
cp /etc/init.d/ssh /etc/init.d/ssh.old && cp -r /etc/ssh /etc/ssh.old
```

## Download

Go to https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/, download the latest version package.

```
wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-8.2p1.tar.gz
tar zxf openssh-8.2p1.tar.gz
```

## Installation

```
./configure --prefix=/usr --sysconfdir=/etc/ssh --with-md5-passwords --with-pam --with-privsep-path=/var/lib/sshd
make -j8
sudo make install

chown 600 /etc/ssh/*key
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/config
```

## Problem

> Job for ssh.service failed because a timeout was exceeded

add code before calling `server_accept_loop`

```cpp
#include <systemd/sd-daemon.h>
...
    /* Signal systemd that we are ready to accept connections */
    sd_notify(0, "READY=1");

    /* Accept a connection and return in a forked child */
    server_accept_loop(&sock_in, &sock_out,
                    &newsock, config_s);
...
```

## Reference

- http://www.linuxfromscratch.org/blfs/view/systemd/postlfs/openssh.html
- https://www.tecmint.com/install-openssh-server-from-source-in-linux/
- http://blog.chinaunix.net/uid-28813320-id-5786956.html
