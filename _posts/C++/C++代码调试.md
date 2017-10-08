---
title: C++ 代码调试
date: 2017-09-26
categories: C++
tags: [c++ gcc]
---

#### 启动 gdb
使用条件：编译时加入 debug 信息，即 `gcc -g`.
进入调试：
```shell
gdb <program> # 直接调试可执行文件
gdb <program> <core dump file> # 查 core
gdb <program> <PID>  # 调试已经启动的程序
```

#### 常用命令
- r : run. 启动程序，直到第一个断点处，或等待用户输入
- c : continue. 继续执行，直到下一个断点处，或等待用户输入
#### 参考资料
- [gdb 调试利器](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html)
- [解决gdb在Mac下不能调试的问题](https://segmentfault.com/a/1190000004136351)