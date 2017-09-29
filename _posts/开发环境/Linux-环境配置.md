---
title: Linux 常用配置
date: 2017-03-28
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
HISTFILESIZE=100000
HISTSIZE=100000
export HISTCONTROL=ignoredups:erasedups # no duplicate entries
shopt -s histappend  # append to history, don't overwrite it
# Save and reload the history after each command finishes
export PROMPT_COMMAND="history -a; history -c; history -r; $PROMPT_COMMAND"
```

> 参考资料：  
> 1. [Preserve bash history in multiple terminal windows](https://unix.stackexchange.com/a/48113/205808)