---
title: Linux 常用配置
date: 2017-03-28
categories: linux
tags: [Linux]
---

#### [prefix sensitive for previous command searching](http://unix.stackexchange.com/a/96511/205808)

在 `~/.inputrc 中添加以下配置:

```shell
# Press up-arrow for previous matching command
"\e[A":history-search-backward
# Press down-arrow for next matching command
"\e[B":history-search-forward
```

#### [自动补全忽略大小写](https://askubuntu.com/a/87066/546909)

, 在`~/.inputrc 中添加以下配置：

```shell
set completion-ignore-case On
```
