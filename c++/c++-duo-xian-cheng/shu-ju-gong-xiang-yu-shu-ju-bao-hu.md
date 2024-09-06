# 数据共享与数据保护

多个线程的执行顺序是乱的，具体执行方法和处理器的调度机制有关系。从开发者的角度讲，就是没有规律的。



在讲数据保护问题之前，为了帮助大家理解数据保护问题，这里额外扩展一些关于汇编的知识

一个进程运行时，数据存储在内存中。如果一个数据要进行运算，必须先将数据拷贝到 寄存器中。比如要对栈上的一个int i进行“++”操作，需要将i的值拷贝到寄存器中，将该 值自加后再拷贝到原来的内存。

如果此时有两个线程均进行的是这样的操作，可能出现两个进程都拷贝了i原来的值到 寄存器，然后各种加一，再拷贝到i对应内存的情况，最终导致i这个变量只自加了一次。&#x20;

这是同时写数据的情况，那么一读一写呢？这也是有问题的，谁知道读数据时写数据步 骤已经到了哪里，谁知道读出来的是个什么东西。



数据保护问题：&#x20;

(1) 数据保护问题总共有三种情况：&#x20;

1. 至少两个线程对共享数据均进行读操作，完全不会出现数据安全问题。&#x20;
2. 至少两个线程对共享数据均进行写操作，会出现数据安全问题，需要数据保护。&#x20;
3. 至少两个线程对共享数据有的进行读，有的进行写，也会出现数据安全问题， 需要进行数据保护。 代



数据保护的方法一共就两种：互斥锁，原子操作。

互斥锁：

(1) 互斥锁的作用原理很简单，对共享数据加锁，当一个线程对这块数据进行操作时， 别的线程就无法对该区域数据进行操作。&#x20;

(2) 这种方式的互斥锁有个弊端，就是lock()之后容易忘记unlock()，就和指针类似。于 是和智能指针类似，也有了lock\_guard，用来防止开发人员忘了解锁



原子操作：（<mark style="background-color:orange;">使用频率远远不及互斥锁</mark>）&#x20;

(3) 原子操作的原理：<mark style="background-color:orange;">将一个数据设置为原子状态，使得该数据处于无法被分割的状态</mark>， 意思就是处理器在处理被设置为原子状态的数据时，其它处理器无法处理该段数据， 该处理器也会保证在处理完该数据之前不会处理其他数据。&#x20;



总结：在编写多线程代码时，数据保护是一个必须考虑，非常常用的功能。互斥锁的使用频率是远远高于原子操作，原子操作看似简单，但当需要保护的数据很多时，就会极其复杂。 所以：<mark style="background-color:orange;">对于单个数据，可以使用原子操作，其它的使用互斥锁就可以了</mark>。





相关代码：多线程的问题

```cpp
#include <iostream>
#include <thread>
#include <mutex>

unsigned g_num = 0;
std::mutex myMutex;

void test()
{
    for (int i = 0; i < 10000000; i++) ++g_num;
}

int main()
{
    std::thread myThread(test);
    for (int i = 0; i < 10000000; i++) ++g_num;

    // join一般都是主线程分出子线程去处理数据，然后主线程等待子线程返回才继续
    // 但其实也可以用来主线程的一部分和子线程并行执行, 此时主线程中创建thread - join这段代码和子线程并行执行
    myThread.join();   

    std::cout << g_num << std::endl;
    return 0;
}


更标准的写法：
如果使用join的话，就是主线程等待子线程的情况，
我们让两个子线程去并行处理数据，而不是让主线程的一部分和子线程去并行处理数据

#include <iostream>
#include <thread>
#include <mutex>

unsigned g_num = 0;
std::mutex myMutex;

void test()
{
    for (int i = 0; i < 10000000; i++) ++g_num;
}

int main()
{
    std::thread myThread1(test);
    std::thread myThread2(test);

    // 更标准的写法，使用join就是主线程等待子线程的情况，我们可以让多个子线程并行处理数据
    // 而不是让主线程的一部分和子线程并行处理，这样的代码不直观
    myThread1.join();
    myThread2.join();
    
    std::cout << g_num << std::endl;
    return 0;
}

```





相关代码：使用互斥锁mutex解决多线程问题

```cpp
#include <iostream>
#include <thread>
#include <mutex>

unsigned g_num = 0;
std::mutex myMutex;


void test()
{
    myMutex.lock();
    for (int i = 0; i < 10000000; i ++) ++g_num;
    myMutex.unlock();
}

int main()
{
    std::thread myThread(test);

    myMutex.lock();
    for (int i = 0; i < 10000000; i ++) ++g_num;
    myMutex.unlock();

    // join一般都是主线程分出子线程去处理数据，然后主线程等待子线程返回才继续
    // 但其实也可以用来主线程的一部分和子线程并行执行, 此时主线程中创建thread - join这段代码和子线程并行执行
    myThread.join();   

    std::cout << g_num << std::endl;
    return 0;
}
```



使用lock\_guard代替mutex

```cpp
#include <iostream>
#include <thread>
#include <mutex>

unsigned g_num = 0;
std::mutex myMutex;

void test()
{
    {
        std::lock_guard<std::mutex> lg(myMutex);
        for (int i = 0; i < 10000000; i++) ++g_num;
        // 以下这种情况很容易会造成没有解锁的操作，所以使用保证开锁的机制 lock_guard; lock_guard需要有个范围，一般用{}包括
        //if (...) {
        //    throw ...
        //}
    }
}

int main()
{
    std::thread myThread(test);
    {
        std::lock_guard<std::mutex> lg(myMutex);
        for (int i = 0; i < 10000000; i++) ++g_num;
    }

    // join一般都是主线程分出子线程去处理数据，然后主线程等待子线程返回才继续
    // 但其实也可以用来主线程的一部分和子线程并行执行, 此时主线程中创建thread - join这段代码和子线程并行执行
    myThread.join();   

    std::cout << g_num << std::endl;
    return 0;
}
```



相关代码 原子操作

```cpp
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<unsigned> g_num = 0;   // 处理单个数据比较方便

void test()
{
    for (int i = 0; i < 10000000; i++) ++g_num;
}

int main()
{
    std::thread myThread1(test);
    std::thread myThread2(test);

    myThread1.join();
    myThread2.join();
    
    std::cout << g_num << std::endl;
    return 0;
}
```
