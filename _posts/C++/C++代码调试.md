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
- f n : 切换到第 n 个栈帧
- i locals : 查看当前栈帧包含的局部变量
- i threads : 查看当前进程的线程
- b : break. 给程序加断点
  - i b : 查看当前所有断点信息
  - b n : 在当前栈帧所属的文件的第 n 行处加断点
  - b fun : 在函数 fun 的入口处加断点
  - d n : 删除断点号 i
  - disable n : 暂时禁用断点 n
  - enable n : 启用断点 n
  - d breakpoints : 删除所有断点
- r : run. 启动程序，直到第一个断点处，或等待用户输入
- c : continue. 继续执行，直到下一个断点处，或等待用户输入
- n : next. 执行下一条语句，不进入函数内部
- s : step. 执行下一条语句，进入函数内部
  - x/i $pc : 显示下一条要执行的指令
  - x/16x 0x7fffffffe8a0 : 显示从地址0x7fffffffe8a0 开始的 16 字节的内存的内容
  - si : 执行下一条指令
  - si n : 执行后 n 条指令
- until : 运行程序知道退出循环
  - until n : 运行至第 n 行
- disassemble /rm : 查看当前栈帧的汇编代码, /r : 显示16进制的指令号; /m : 显示相应的源码
- i registers : 打印当前寄存器的值
  - i registers rbp rsp : 显示寄存器 rbp，rsp 的值
- set $rsp -= 0x10 : 设置寄存器 rsp 的值
- set $tmp = 0 : 设置临时变量 tmp
- k : kill. 杀掉进程
- shell ls : 执行 shell 命令
- p var : 打印变量 var 的值
- whatis var : 显示变量 var 的类型
- pt var : ptype. 显示变量 var 的类型的定义
- 

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