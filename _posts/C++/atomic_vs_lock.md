---
title: C++ atomic, lock 性能对比
date: 2018-02-27
categories: C++
tags: [c++]
---

下面的代码用 atomic 和 lock 分别实现了线程安全的计数器, 可以看到 atomic 的性能比 lock 提升了数十倍.
正常开发中可以使用C++ 标准库中的 atomic<int>, 具有更高的性能, 比 lock 高百倍 

```

```
