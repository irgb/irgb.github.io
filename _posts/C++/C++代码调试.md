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

- bt : backtrace. 显示当前调用栈
- b : break. 给程序加断点
  - b n : 在当前栈帧所属的文件的第 n 行处加断点
  - b fun : 在函数 fun 的入口处加断点
- r : run. 启动程序，直到第一个断点处，或等待用户输入
- c : continue. 继续执行，直到下一个断点处，或等待用户输入
- n : next. 执行下一条语句，不进入函数内部
- s : step. 执行下一条语句，进入函数内部 
- until : 
- shell ls : 执行 shell 命令

#### [启用gdb命令历史](https://stackoverflow.com/a/3176802/5432806)
在 ~/.gdbinit 中添加：

```shell
set history filename ~/.gdb_history
set history save on
set history size 1000
```

#### 参考资料
- [gdb 调试利器](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html)
- [](http://xuwenjie.blog.51cto.com/6978573/1192028)
- [](https://blogs.gnome.org/raywang/page/23/)
- [](http://www.cnblogs.com/xsln/p/gdb_instructions1.html)
- [解决gdb在Mac下不能调试的问题](https://segmentfault.com/a/1190000004136351)