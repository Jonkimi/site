---
title: Install-ohmyzsh-fzf-z
date: 2019-11-29 10:34:14
tags: [zsh, ohmyzsh, fzf, z]
description: Install oh-my-zsh with fzf and z
---

## Install zsh

```shell
brew install zsh
```
TabNine::config
CentOS
```shell
sudo yum update && sudo yum -y install zsh
```

## Install ohmyzsh

```shell
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
zsh-autosuggestions
```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

zsh-syntax-highlighting
```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

```zshrc
plugins=(
  git
  z
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```

## Install fzf

```shell
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

```zshrc
export FZF_BASE=~/.fzf
plugins=(
  git
  fzf
  z
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```

## Install zplug

```shell
curl -sL --proto-redir -all,https https://raw.githubusercontent.com/zplug/installer/master/installer.zsh | zsh
```

MacOS
```shell
brew install zplug
```

```zshrc
export ZPLUG_HOME=/path/to/.zplug
source $ZPLUG_HOME/init.zsh
zplug "changyuheng/fz", defer:1
zplug load
```

```shell
source ~/.zshrc
zplug install
source ~/.zshrc
```



## Reference

- https://github.com/ohmyzsh/ohmyzsh
- https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH
- https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh
- https://github.com/zsh-users/zsh-syntax-highlighting
- https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md
- https://github.com/junegunn/fzf
- https://github.com/zplug/zplug