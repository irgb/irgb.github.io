---
title: Linux 常用配置
date: 2017-03-28
categories: 开发环境
tags: [Linux]
---

#### `~/.inputrc` 配置
```shell
# [Tab auto-completion case-insensitive](https://askubuntu.com/a/87066/546909)
set completion-ignore-case On
# [prefix sensitive for previous command searching](http://unix.stackexchange.com/a/96511/205808)
# Press up-arrow for previous matching command
"\e[A":history-search-backward
# Press down-arrow for next matching command
"\e[B":history-search-forward
```

#### `~/.bashrc`配置

, 在`~/.inputrc 中添加以下配置：

```shell
# colorful command line prompt
export PS1="\[\e[37;40m\][\[\e[32;40m\]\u\[\e[33;40m\]@\[\e[32;40m\]\H \[\e[33;40m\]\w\[\e[37;40m \]]\\$ \[\e[37;0m\]"
# colorful ls result
alias ls='ls --color'
# increase command listory to 10000 items
HISTFILESIZE=10000
HISTSIZE=10000
```
