### 安装
1. 下载 [gperftools-2.0.tar.gz](https://github.com/gperftools/gperftools/archive/gperftools-2.0.tar.gz)
2. 执行以下命令安装:
```shell
tar zxvf gperftools-2.0.tar.gz
cd gperftools-2.0
export PATH=/opt/compiler/gcc-4.8.2/bin:$PATH
./configure --prefix=~/gperftools --enable-frame-pointers
make && make install # 如果编译不过，可尝试将configure生成的Makefile CXXFLAGS = -g -O2 配置改为 CXXFLAGS = -g -O2 -fpermissive
export PATH=~/gperftools/bin:$PATH
```
### CPU Profiler
用来分析性能瓶颈, 监控各函数的 cpu 耗时.
1. 添加头文件 `#include "gperftools/profiler.h"`或 `#include "google/profiler.h"`
2. 在要监控的代码开头位置添加 `ProfilerStart("cpu.perf");`, 在结尾处添加`ProfilerStop();` 即可.
3. 编译时链接指定链接参数`-I~/gperftools/include -L~/gperftools/lib -lprofiler`
4. 程序执行结束之后, 文件夹下会出现`cpu.perf` 文件, 用命令`pprof --text /path/to/binary cpu.prof` 用文本形式查看结果.

> 如果使用`--gv` 生成图形化结果, 需要安装`graphviz`(其中包含了 dot  绘图工具) 和 gv.

**参考**
- [cpuprofile](https://gperftools.github.io/gperftools/cpuprofile.html)

### Heap Profiler
1. 添加头文件`#include "gperftools/heap-profiler.h"`或 `#include "google/heap-profiler.h"`
2. 在要监控的代码开头位置添加 `HeapProfilerStart("heap.perf");`, 在结尾处添加`HeapProfilerStop();` 即可.

**参考**
- [heapprofile](https://gperftools.github.io/gperftools/heapprofile.html)
