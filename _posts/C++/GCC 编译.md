---
title: GCC4.8.2 编译问题
date: 2017-09-26
categories: C++
tags: [c++ gcc]
---

## 发生类型转换错误：
如果指定了-Werror 会把所有的 warning 当做 error。如果要抑制部分 warning，如unsigned int 转 int 等，可以添加编译参数 `-Wno-error=sign-conversion`。

> 参考：  
> [GCC Warning-Options](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html)  
> [C++ Implicit Conversion](http://en.cppreference.com/w/cpp/language/implicit_conversion)