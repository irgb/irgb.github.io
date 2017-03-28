---
title: Linux 环境配置
date: 2017-03-28
categories: linux
tags: [Linux]
---

### [prefix sensitive for previous command searching](http://unix.stackexchange.com/a/96511/205808)

在 `~/.inputrc 中添加以下配置:

```shell
# Press up-arrow for previous matching command
"\e[A":history-search-backward
# Press down-arrow for next matching command
"\e[B":history-search-forward
```

