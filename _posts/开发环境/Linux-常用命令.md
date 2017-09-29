---
title: Linux 常用命令
date: 2017-03-29
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

#### 查看 jar 包内容
```shell
jar -tf test.jar
```

#### 统计文件行数或单词数
```shell
wc -l /path/to/file # line count
wc -w /path/to/file # word count
```

#### 获取文件指定行
```
sed -n 5,10p /path/to/file
```
> 获取文件的第5-10行

#### 获取文本的第 k 列
```shell
cut -d '\t' -f 1 total # cut 只支持单个字符分割，默认是\t
awk '{print $9}' # awk 默认可以分割连续的空格
awk -F ',' '{print $1, $2}' /path/to/file # 按照字符','进行分割，并打印第1，2列
awk -F '[, ;]' '{print $1}' /path/to/file # 按照',', ' ' 或 ';' 进行分割
awk -F 'SEP' '{print $1}' /path/to/file # 按照字符串“SEP”进行分割
```

例如：

```shell
echo "word1SEPword2" | awk -F 'SEP' '{print $1, $2}'
```
> word1 word2

```shell
echo 'w1  ,; w2  ,,   w4' | awk -F '[[:blank:],;]+' '{print NF}'
```
> 3  
> 按照连续的空白、',', ';'分割，NF 表示 Number of fields。表示分割之后的 field 的数目。  
> 参考：[正则表达式](http://dcx.sybase.com/1101/zh/dbreference_zh11/rf-sqllanguage-s-4915351.html)


#### 文本行去重，相同行数统计

```shell
echo "
This is line1
This is line1
This is line2
This is line2
This is line2" | uniq
```

> 结果为：  
> This is line1  
> This is line2  
> `uniq -c` 表示统计形同行的数目

例子：
```shell
echo "word1 word1 word2 word2 word2" | xargs -n1 | uniq -c
```
> 一个简单的 wordcount 程序。其中 `xargs -n1` 用于把每个单词转成一行。

#### 文件排序
```shell
sort /path/to/file # 对文本行进行字符串排序
sort -k 2 /path/to/file # 按照第2列排序，默认分隔符为' ', '\t'，注意支持单个字符
sort -k 2 -t ';' /path/to/file # 用';'作为分隔符，并按第2列排序
```

> -g : 按照实数排序，而不是字符串  
> -r : 倒序排列
> -h : 可以排序以 K, M, G 为单位的数字，只有新版本的 sort 支持这个参数

例子：
```
ll --color=never | awk '{print $9}' | xargs du -s | sort -k 1 -r -g
```
> 功能：对当前目录下的所有文件夹从大到小排序  
> --color : 去掉 ll 或 ls 命令的颜色  

#### 文本替换
```shell
sed 's/word1/word2/g' /path/to/file # 将文件中所有的 word1 替换成 word2，并将结果打印到终端
sed -i 's/word1/word2/g' /path/to/file # in-place replace
sed -i'.bak' 's/word1/word2/g' /path/to/file # in-place replace and back up original file
```

例子：
```shell
sed -i '/^$/N;/^\n$/D' /path/to/file # 删除多余的空行
```

> 更多关于 sed 的用法参考：
> [15 Useful ‘sed’ Command Tips and Tricks](https://www.tecmint.com/linux-sed-command-tips-tricks/)

#### 比较两个命令的执行结果有什么不同
```shell
diff <(command1) <(command2)
```
> diff 命令只能比较两个文件的不同，所以为了比较两个命令的输出，要把输出转换成一个文件名传递给 diff，这种技术叫做“Process Substitution”，参考：  
> [How do I diff the output of two commands](https://askubuntu.com/a/229450/546909)  
> [Process Substitution](http://www.gnu.org/software/bash/manual/bash.html#Process-Substitution)

例子：
```shell
diff <(ack "word1" | cut -d ':' -f1 | uniq) <(ack "word2" | cut -d ':' -f1 | uniq) # 比较包含 word1 的文件和包含 word2 的文件有哪些不同
```

#### 查看端口占用情况
```shell
sudo lsof -i:8080
sudo netstat -ap | grep 8080
```