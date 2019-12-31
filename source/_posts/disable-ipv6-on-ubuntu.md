---
title: Disable IPV6 on Ubuntu
tags: [ubuntu, ipv6]
description: Disable IPV6 on Ubuntu
date: 2019-12-31 11:26:27
---


## Disable IPv6 in APT

```shell
echo 'Acquire::ForceIPv4 "true";' | sudo tee /etc/apt/apt.conf.d/99force-ipv4
```

## Disable IPv6 on all

```shell
sudo tee -a /etc/sysctl.d/99-sysctl.conf > /dev/null << EOL
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
EOL
sudo sysctl -p
```

## Reference

- https://www.linuxbabe.com/ubuntu/disable-ipv6-on-ubuntu