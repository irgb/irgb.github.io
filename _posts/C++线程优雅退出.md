---
title: C++ 线程优雅退出
date: 2018-05-01
categories: C++
tags: [c++]

---

### C++ 线程如何优雅退出(执行清理操作)
多线程程序中, 经常会定时执行任务. 通常的做法是, 在 while 循环中执行一个 task, 然后 sleep 一段时间. 如下:
```cpp
#include <time.h>
#include <unistd.h>
#include <signal.h>
#include <iostream>
#include <thread>

bool is_stopped = false;

void nanotask() {
    struct timespec interval;
    interval.tv_sec = 10;
    interval.tv_nsec = 0;
    while(!is_stopped) {
        std::cout << "nanosleeping" << std::endl;
        nanosleep(&interval, NULL);
        std::cout << "wake up" << std::endl;
    }
}

void task() {
    while(!is_stopped) {
        std::cout << "sleeping" << std::endl;
        // sleep 底层实现还是 nanosleep
        sleep(10);
        std::cout << "wake up" << std::endl;
    }
    std::cout << "cleanup" << std::endl;
}

void handler(int sig) {
    std::cout << "got signal:" << sig << std::endl;
    // 虽然设置了is_stopped = true, 但是要等到 sleep 返回, 程序才能结束
    is_stopped = true;
}

int main() {
    // 谨慎使用 signal, 尽量使用 sigaction
    struct sigaction sa;
    sa.sa_handler = handler;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sigaction(SIGTERM, &sa, NULL);
    sigaction(SIGINT, &sa, NULL);

    std::thread t1(task);
    std::thread t2(nanotask);
    t1.join();
    t2.join();
    std::cout << "thread exit" << std::endl;
    return 0;
}
```
这段程序用 sleep 或 nanosleep 作为时间间隔, 并监听 SIGINT(ctrl + c) 和 SIGTERM(kill <pid>) 两个信号. 但有以下几个问题:
1. sleep 和 nanosleep 无法被唤醒, 所以程序再接收到 SIGINT 或 SIGTERM 之后必须等待sleep 正常返回才能执行后面的 cleanup 代码. 
2. 如果不监听 SIGINT 或 SIGTERM, 那么系统会执行默认的 handler, 并终止程序, 这会 interrupt sleep 函数, 但也会导致 cleanup 代码被跳过.
3. sleep 是linux 系统调用(包含在 unistd.h 中), 其实现与平台相关的, 所以可移植性不好. 所以有些实现是[用可移植的 select 函数代替 sleep](https://stackoverflow.com/a/3125701/5432806). 但这同样会面临[无法被唤醒的问题](https://stackoverflow.com/a/264378/5432806).

-----------------------------------------------------------------------------------------

**更好的实现方式是利用C++11 中的 mutex 和 condition_variable**. 利用condition_variable::wait_for 实现可 interruptible 的 sleep 功能. 正常情况下 wait_for 超时, 接收到退出信号之后, 程序会立即被唤醒, 退出 while 循环, 并执行 cleanup 代码.

```cpp
#include <signal.h>
#include <thread>
#include <condition_variable>
#include <mutex>
#include <chrono>
#include <iostream>

class InterruptibleSleeper {
public:
    // returns false if killed:
    template<typename R, typename P>
    bool wait_for(std::chrono::duration<R, P> const& time ) {
        std::unique_lock<std::mutex> lock(m);
        return !cv.wait_for(lock, time, [&]{return terminate;});
    }

    void interrupt() {
        std::unique_lock<std::mutex> lock(m);
        terminate = true;
        cv.notify_all();
    }
private:
    std::condition_variable cv;
    std::mutex m;
    bool terminate = false;
};

InterruptibleSleeper sleeper;

void task() {
    while(sleeper.wait_for(std::chrono::milliseconds(30000))) {
        std::cout << "working" << std::endl;
    }
    std::cout << "cleanup" << std::endl;
}

void handler(int sig) {
    std::cout << "got signal:" << sig << std::endl;
    // 执行 interrupt 之后, 休眠中的线程会被唤醒, 并执行 cleanup 操作
	sleeper.interrupt();
}

int main() {
    // 谨慎使用 signal, 尽量使用 sigaction
    struct sigaction sa;
    sa.sa_handler = handler;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sigaction(SIGTERM, &sa, NULL);
    sigaction(SIGINT, &sa, NULL);

    std::thread t(task);
    t.join();
    std::cout << "thread exit" << std::endl;
	return 0;
}
```
**Reference :**
- [stopping long sleep threads](https://stackoverflow.com/questions/29775153/stopping-long-sleep-threads)
