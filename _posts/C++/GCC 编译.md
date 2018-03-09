---
title: gcc / g++编译
date: 2017-09-26
categories: C++
tags: [c++ gcc]

---

### gcc、g++ 常用编译参数
- -E : 预处理，处理结果输出到标准输出，如果需要输出到文件，需要使用 -o 参数，通常用 '.i' 作为后缀.
- -S : 编译，生成汇编代码。默认输出到文件，后缀为 '.s'.
- -c : 汇编，把C/C++代码或汇编代码转成可执行文件，默认输出到文件，后缀为 '.o'.
- -o : 指定目标文件名，可用于编译的各个阶段.
> 从 C/C++ 代码到可执行文件分成四个步骤：预处理、编译、汇编、链接，可以用参数来一步步执行。

- -O : 指定编译优化级别，共 0~3 四个级别, 默认为 '-O0', 即不用任何优化，'-O' 等价于 '-O1'. 参考：[GCC optimization levels](https://stackoverflow.com/a/1778700/5432806)
- -g : 在生成的可执行文件中加入调试信息。
- -L : 指定库所在的目录
- -l : 指定链接时要链接的库（注意，库文件 libtest.a 的库名是 test）
> 如库 libtest.a 保存在目录 '/home/lib' 下，编译的时候需用命令 `g++ -L/home/lib -ltest main.cpp -o main`, 否则会出现链接错误。

- -i : 指定所需的头文件。
- -I : 指定所需的头文件所在的目录。g++可以自动在目录中搜索需要的头文件，所以 '-i' 参数很少使用.
- -Wall : 显示所有编译警告信息
- -Werror : 把所有警告当做错误处理
- -w : 关闭所有警告信息

#### 参考资料：
- [What is the difference between g++ and gcc?](https://stackoverflow.com/questions/172587/what-is-the-difference-between-g-and-gcc)
- [gdb - How to view a pointer like an array?](https://stackoverflow.com/a/14502287/5432806)
- [GCC Warning-Options](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html)

### 编译到静态库或动态库
静态库只是一些目标文件的集合.
```shell
// 编译目标文件 .o
gcc -c file.c
g++ -c file.cpp
// 打包成静态库
ar cr libmalloc_wrap.a malloc_wrap.o
// 打包成动态库
gcc -fPIC -shared -o libfile.so ./file.c -ldl
```
**注意**: 在编译动态库时, 一定要指定-fPIC 生成位置无关代码.

### 常见编译问题
#### 类型转换错误：
如果指定了-Werror 会把所有的 warning 当做 error。如果要抑制部分 warning，如unsigned int 转 int 等，可以添加编译参数 `-Wno-error=sign-conversion`。

> 参考：
> - [C++ Implicit Conversion](http://en.cppreference.com/w/cpp/language/implicit_conversion)
