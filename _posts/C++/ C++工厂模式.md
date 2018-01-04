---
title: C++ 工厂模式
date: 2018-01-04
categories: C++
tags: [c++ design-pattern factory-pattern]
---

#### 用途

工厂模式经常用来代码解耦。常用于插件系统，如下图所示：
<img src="/images/factory_pattern_file_dependency.jpg" height=200, width=300/>
用户只需要创建新的 plugin 即可，而不需要修改代码的其它部分。程序在启动的时候会自动把所有 plugin 注册到 factory 中，主程序中根据用户参数就可以创建指定的 plugin 对象。

#### 源码

**main.cpp**

```cpp
#include "base.h"
#include "factory.h"

using namespace std;
//before entering main function, all global (static) variables will be initialized, in which process plugins are registered to factory.
int main() {
    Factory & factory = Factory::Instance();
    //p1, p2 are unique_ptr<Base>, so caller own new created plugin object.
    auto p1 = factory.Create("Plugin1");
    auto p2 = factory.Create("Plugin2");
    auto p3 = factory.Create("Plugin3");
    if (p1) {
        p1->do_something();
    }
    if (p2) {
        p2->do_something();
    }
    if (p3) {
        p3->do_something();
    }
}
```

**factory.h**

```cpp
#ifndef _FACTORY_H
#define _FACTORY_H

#include <functional>
#include <string>
#include <unordered_map>

#include "base.h"

class Factory {
public:
    static Factory& Instance() {
        static Factory singleton;
        return singleton;
    }

    typedef std::function<Base*()> Creator;
    // ATTENTION: can not use register as function name, becase register is a keyword in C++11
    bool Register(const std::string & plugin_name, const Creator & creator) {
        auto ret = _map.insert(std::make_pair(plugin_name, creator));
        return ret.second;
    }
    // do not return raw pointer directly, use smart pointer instead to transfer ownership to caller
    std::unique_ptr<Base> Create(const std::string & plugin_name) {
        auto iter = _map.find(plugin_name);
        if (iter == _map.end()) return std::unique_ptr<Base>(nullptr);
        return std::unique_ptr<Base>((iter->second)());
    }
private:
    std::unordered_map<std::string, Creator> _map;
    Factory() {}

    // forbid copy constructor and assign operation,
    // to make sure Factory is a Singleton
    Factory(const Factory &) = delete;
    Factory & operator = (const Factory &) = delete;
    // forbid move constructor and move assign operation,
    // to make sure Factory is a Singleton
    Factory(Factory &&) = delete;
    Factory & operator = (Factory &&) = delete;
};

template<typename Derived>
Base* Creator() {
    return new Derived();
}

// macro is used widely in many factory pattern implementation. But, macro is ugly, it make program hard to comprehend and debug, so avoid using it
#define REGISTER_PLUGIN(CLASSNAME) \
    namespace { \
        static const auto CLASSNAME##register_result = Factory::Instance().Register(#CLASSNAME, Creator<CLASSNAME>); \
    } \

#endif
```

**base.h**

```cpp
#ifndef _BASE_H
#define _BASE_H
class Base {
public:
    virtual void do_something() = 0;
};
#endif
```

**plugin1.cpp**

```cpp
#include <iostream>
#include "base.h"
#include "factory.h"

class Plugin1 : public Base {
    void do_something() {
        std::cout << "Plugin1" << std::endl;
    }
};
//static const auto register_result1 = Factory::Instance().Register("Plugin1", Creator<Plugin1>);
REGISTER_PLUGIN(Plugin1);
```

**plugin2.cpp**

```cpp
#include <iostream>
#include "base.h"
#include "factory.h"

class Plugin2 : public Base {
    void do_something() {
        std::cout << "Plugin2" << std::endl;
    }
};
//static const auto register_result2 = Factory::Instance().Register("Plugin2", Creator<Plugin2>);
REGISTER_PLUGIN(Plugin2);
```

**编译运行**
上面的代码中再 Factory 中记录了所有 Plugin 的创建方法，每次 create 都会返回一个新的对象,
将所有文件放到一个文件夹下，执行以下命令:
```shell
g++ -std=c++11 main.cpp plugin1.cpp plugin2.cpp -o main
./main
```

需要注意的是：
1. 上面的代码中 Factory 只能注册 Base 的子类，如果要实现通用的 Factory，可以给 Factory 加上模板。
2. 这里的实现和 [Compile-Time Plugin System(github)](https://gist.github.com/Cilyan/a8117124b04b64642646) 中的实现是一致的，对应关系为：

 - Factory <=> PluginFactory
 - Creator <=> IPluginRegistrar
 - Base <=> IPlugin
 - Plugin1, Plugin2 <=> Plugin1


#### Reference

- [C++ loop over all subclasses of an Abstract class?
](https://stackoverflow.com/a/5451094/5432806)
- [Compile-Time Plugin System(github)](https://gist.github.com/Cilyan/a8117124b04b64642646)
- [Compile-time plugin system(stackoverflow)](https://codereview.stackexchange.com/questions/119812/compile-time-plugin-system)
