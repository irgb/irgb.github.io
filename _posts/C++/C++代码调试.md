---
title: C++ 代码调试
date: 2017-09-26
categories: C++
tags: [c++ gcc]
---

#### gdb
使用条件：编译时加入 debug 信息，即 `gcc -g`.
启动调试：
```shell
gdb <program>
gdb <program> <core dump file> # 查 core
gdb <program> <PID>  # 调试已经启动的程序
```
#### 参考资料
- [解决GDB在Mac下不能调试的问题](https://segmentfault.com/a/1190000004136351)