---
title: C++ 代码调试
date: 2018-01-04
categories: C++
tags: [c++ design-pattern factory-pattern]
---

#### 用途
工厂模式经常用来代码解耦。常用于插件系统，如下图所示：
![](/images/factory_pattern_file_dependency.jpg)
用户只需要创建新的 plugin 即可，而不需要修改代码的其它部分。程序在启动的时候会自动把所有 plugin 注册到 factory 中，主程序中根据用户参数就可以创建指定的 plugin 对象。
