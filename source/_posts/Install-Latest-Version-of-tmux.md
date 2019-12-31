---
title: Install Latest Version of tmux
tags: [tmux, Tool]
description: Install latest version of tmux
date: 2019-12-23 10:44:28
---


## Commands

```shell
sudo apt-get install -y git libevent-dev libncurses-dev make
git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure && make
sudo make install
```

## Reference

- https://bogdanvlviv.com/posts/tmux/how-to-install-the-latest-tmux-on-ubuntu-16_04.html