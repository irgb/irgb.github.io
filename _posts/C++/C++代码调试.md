---
title: C++ 代码调试
date: 2017-09-26
categories: C++
tags: [c++ gcc]
---
#### gcc、g++ 常用编译参数
- -E : 预处理，处理结果输出到标准输出，如果需要输出到文件，需要使用 -o 参数，通常用 '.i' 作为后缀.
- -S : 编译，生成汇编代码。默认输出到文件，后缀为 '.s'.
- -c : 汇编，把C/C++代码或汇编代码转成可执行文件，默认输出到文件，后缀为 '.o'.
- -o : 指定目标文件名，可用于编译的各个阶段.
> 从 C/C++ 代码到可执行文件分成四个步骤：预处理、编译、汇编、链接，可以用参数来一步步执行。  

- -O : 指定编译优化级别，共 0~3 四个级别, 默认为 '-O0', 即不用任何优化，'-O' 等价于 '-O1'. 参考：[GCC optimization levels](https://stackoverflow.com/a/1778700/5432806)
- -g : 在生成的可执行文件中加入调试信息。
- -L : 指定库所在的目录
- -l : 指定链接时要链接的库
> 如库 libtest.a 保存在目录 '/home/lib' 下，编译的时候需用命令 `g++ -L/home/lib -ltest main.cpp -o main`, 否则会出现
#### gdb
使用条件：编译时加入 debug 信息，即 `gcc -g`.


#### 参考资料
- [What is the difference between g++ and gcc?](https://stackoverflow.com/questions/172587/what-is-the-difference-between-g-and-gcc)
- [gdb - How to view a pointer like an array?](https://stackoverflow.com/a/14502287/5432806)
- [解决GDB在Mac下不能调试的问题](https://segmentfault.com/a/1190000004136351)