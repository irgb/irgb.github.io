---
title: C++ 代码调试
date: 2017-09-26
categories: C++
tags: [c++ gcc]
---
#### gcc、g++ 常用编译参数
- -E : 只对输入文件进行预处理，处理结果输出到标准输出，如果需要输出到文件，需要使用 -o 参数
- -S : 
#### gdb
使用条件：编译时加入 debug 信息，即 `gcc -g`.



#### 参考资料
- [What is the difference between g++ and gcc?](https://stackoverflow.com/questions/172587/what-is-the-difference-between-g-and-gcc)
- [gdb - How to view a pointer like an array?](https://stackoverflow.com/a/14502287/5432806)
- [解决GDB在Mac下不能调试的问题](https://segmentfault.com/a/1190000004136351)