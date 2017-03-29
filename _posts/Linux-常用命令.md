---
title: Linux 常用命令
date: 2017-03-29
categories: linux
tags: [Linux]
---

#### 显示任意路径下所有文件夹的大小
下面的命令显示 `/var` 下所有文件夹的大小:

```shell
ls --color=none -lhd /var/* | awk '$1~/^d/ {print $9}' | xargs sudo du -sh`
```
