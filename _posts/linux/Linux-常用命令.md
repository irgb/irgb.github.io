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

#### 按照某一列进行排序
下面的命令对当前文件夹下的所有文件进行降序排序

```shell
ls -alhS # -h means --human
# or
ls --sort=size -alh
# or
ls -al | sort -k 5 -nr # -n 按照字符串的数值比较大小, 会忽略 K, M, G 等单位
# or
ls -alh | sort -k 5 -hr # 支持 K, M, G 等 human-kind 单位
```

#### 编码转换
```shell
iconv -f UTF-8 -t GBK -c /path/to/file
```
> -s : silences warnings  
> -c : omits invalid characters from output  
> -f : source encoding  
> -t : target encoding  

#### 带颜色的命令行提示符
```shell
export PS1="\[\e[37;40m\][\[\e[32;40m\]\u\[\e[33;40m\]@\[\e[32;40m\]\H \[\e[33;40m\]\w\[\e[37;40m \]]\\$ \[\e[37;0m\]"
alias ls='ls --color'
```

#### 修改 history 命令的最大条数
在`~/.bashrc`中添加以下配置
```shell
HISTFILESIZE=1000
HISTSIZE=1000
```

####  使TAB 键的命令自动补全忽略大小写
在`~/.inputrc`中添加：
```shell
set completion-ignore-case On
```

