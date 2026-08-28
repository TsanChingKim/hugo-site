---
title: "Mutex Lock"
date: 2026-08-28
description: "详细讲解了Mutex锁的概念及使用"
tags: ["c++", "boost", "q&a"]
showTableOfContents: true
---

# Mutex 锁
## 一、`boost::recursive_mutex`的概念
### 1.普通互斥量
普通互斥量（`boost::mutex`/`std::mutex`）不允许同一个线程重复对同一把锁加锁，否则会导致死锁（deadlock）。
```c++
boost::mutex m;
void func(){
	boost::lock_guard<boost::mutex> lock(m);
  // 如果同一个线程再次加锁
  boost::lock_guard<boost::mutex> lock2(m);	// 错误
}
```
普通互斥量的内部逻辑是这样的：
```c++
void lock() {
    while (true) {
        if (try_lock()) return;   // CAS: 把标志位 0→1，成功则拿到锁
        // 标志位已经是 1 → 阻塞当前线程，进入睡眠，等待被唤醒
        block_and_wait();
    }
}
```
使用现实中的动作来进行类比的话，将lock（加锁）视为拿钥匙，将unlock（释放锁）视为放下钥匙。当一个人拿起钥匙（lock）来开门之后，在放下钥匙之前，它再次去拿钥匙，它只知道有人拿了钥匙，并不知道钥匙就在自己的手里，所以它会持续等待拿了要是的人把钥匙放回来，就导致这个程序阻塞了。

{{< mermaid >}}
graph TD
	A[拿取钥匙] --> B[开门]
  B --> D[自己又去拿钥匙]
  D --> E["发现钥匙不在，等待那要是的人将钥匙拿回来"]
  E --> E
  E -.-> F[放下钥匙]
{{< /mermaid >}}

### 2.递归互斥量
`boost::recursive_mutex`允许同一个线程多次加锁，只要解锁次数与加锁次数相同即可
```c++
boost::recursive_mutex rm;
void func(){
	boost::lock_guard<boost::recursive_mutex> lock(rm);
  // 同一个线程可以再次加锁
  boost::lock_guard<boost::recursive_mutex> lock2(rm);
}
```
### 3.为什么需要递归互斥量？
最常见的场景是递归调用：一个函数内部调用了另一个也需要加锁的函数，而这两次加锁发生在同一个线程上。
```c++
class Counter {
    mutable boost::recursive_mutex mu;
    int count = 0;
public:
    void increment() {
        boost::lock_guard<boost::recursive_mutex> lock(mu);
        ++count;
    }
    void incrementTwice() {
        boost::lock_guard<boost::recursive_mutex> lock(mu);
        increment(); // 这里再次对同一把锁加锁
        increment();
    }
};
```
## 二、认识`scoped_lock`
`scoped_lock`是互斥量内部定义的一个RAII锁类的别名（typedef)。它本质上是一个作用域锁包装器：**构造时自动加锁，析构时自动解锁。**
在 Boost.Thread 中，`boost::recursive_mutex::scoped_lock`通常等价于：
```c++
typedef boost::unique_lock<boost::recursive_mutex> scoped_lock;
```
### 特点
- 自动管理锁的生命周期：它被创建时，会立刻试图获取锁；当离开作用域时（无论是正常结束还是异常抛出），析构函数会自动释放锁。
- 不可复制：scoped_lock 对象通常不可复制、不可赋值，只能移动（unique_lock 可移动）。
- 可配合条件变量：如果是 unique_lock 的别名，还可以和 boost::condition_variable 一起使用，因为 unique_lock 支持手动解锁/加锁.
### 使用方式
```c++
#include <boost/thread/recursive_mutex.hpp>

boost::recursive_mutex global_mutex;

void foo() {
    boost::recursive_mutex::scoped_lock lock(global_mutex);
    // 在这里访问共享数据，安全
    // 函数结束或发生异常时，lock 析构，自动解锁
}
```
## 三、完整示例代码
下面一个使用`boost::recursive_mutex::scoped_lock`保护递归函数的例子：
```c++
#include <iostream>
#include <boost/thread/recursive_mutex.hpp>

class Database {
    mutable boost::recursive_mutex mu;
    int data = 0;
public:
    // 插入一条记录（内部调用私有函数）
    void insert() {
        // 加锁，作用域结束自动解锁
        boost::recursive_mutex::scoped_lock lock(mu);
        doInsert();
    }

    // 批量插入，也会调用 insert()
    void batchInsert(int n) {
        boost::recursive_mutex::scoped_lock lock(mu);
        for (int i = 0; i < n; ++i) {
            insert(); // 在已经持锁的情况下再次调用 insert()
        }
    }

    void show() const {
        boost::recursive_mutex::scoped_lock lock(mu);
        std::cout << data << std::endl;
    }

private:
    void doInsert() {
        // 这里也假定需要锁保护
        ++data;
    }
};

int main() {
    Database db;
    db.batchInsert(3);
    db.show();  // 输出 3
    return 0;
}
```
## 四、与其他锁的对比
|锁类型|是否允许同一线程重复加锁|用途|
|:--:|:--:|:--:|
|`boost::mutex/std::mutex`|不允许|普通并发保护|
|`boost::recursive_mutex/std::recursive_mutex`|允许|递归函数、需要嵌套加锁的场景|
|`boost::mutex::scoped_lock`|取决于 mutex|RAII 锁包装|
|`std::scoped_lock`（C++17）|可同时锁多个互斥量|防止死锁的多锁场景|

> 注意：
> 1.递归互斥量有额外的性能开销，因为需要追踪的所有者和加锁次数。如果并不需要递归加锁，**尽量使用普通互斥量**。
> 2.每个`scoped_lock`对象控制一个锁的持有时间。不要在函数中间手动`unlock`（除非使用`unique_lock`），否则容易出错。
> 3.虽然`recursive_mutex`避免了"同一线程重复加锁"的死锁，但仍可能与其他锁产生**交叉等待死锁。**
> 4.即使是递归锁，也建议尽量缩小临界区，避免在持锁时进行耗时操作（如IO），以提高并发性。
> 5.常见的错误是忘记用花括号限制作用域，导致锁提前释放。
{.is-danger}



