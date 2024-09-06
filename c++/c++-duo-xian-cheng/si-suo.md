# 死锁

死锁就像两个人在互相等对方。A说，等B来了就去B现在所在的地方；B说，等A来了我 就去A所在的地方，结果就是A和B都在等对面过来才能去对面。这就导致了一个死循环， 放在多线程中，就是死锁。&#x20;



解决方法也很简单。

(1) 只要让两个锁顺序一致就可以了。 代码演示：

(2) 但是让两个锁顺序一致常常是说起来容易，做起来难。于是C++11提供了std::lock。这个 模板可以保证多个互斥锁绝对不会出现死锁的问题。同时提供了std::adopt\_lock的功能来避 免忘记释放锁的问题。&#x20;



总结：死锁是一个比较常见的bug，面试时也经常询问死锁相关的知识。&#x20;



相关代码：死锁问题，常是由加锁顺序导致

```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex myMutex1;
std::mutex myMutex2;

unsigned g_num = 0;

void test()
{
    for (int i = 0; i < 10000000; i++) {
        // 嵌套使用锁 lg1, lg2
        std::lock_guard<std::mutex> lg1(myMutex1);
        std::lock_guard<std::mutex> lg2(myMutex2);
        ++g_num;
    }
}


int main()
{
    std::thread myThread(test);

    for (int i = 0; i < 10000000; i++) {
        // 嵌套使用锁lg2，lg1
        std::lock_guard<std::mutex> lg2(myMutex2);
        std::lock_guard<std::mutex> lg1(myMutex1);
        ++g_num;
    }
    // 主线程中创建thread到join的这部分和子线程并行
    myThread.join();

    std::cout << g_num << std::endl;

    return 0;
}
```





相关代码：终极解决死锁的方案 lock + std:adopt\_lock

```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex myMutex1;
std::mutex myMutex2;

unsigned g_num = 0;

void test()
{
    for (int i = 0; i < 10000000; i++) {
           
        std::lock(myMutex1, myMutex2);   // 包含多个锁

        std::lock_guard<std::mutex> lg1(myMutex1, std::adopt_lock);   // adopt_lock只负责解锁，没有枷锁功能了
        std::lock_guard<std::mutex> lg2(myMutex2, std::adopt_lock);
        ++g_num;
    }
}


int main()
{
    std::thread myThread(test);

    for (int i = 0; i < 10000000; i++) {
        std::lock(myMutex1, myMutex2);

        std::lock_guard<std::mutex> lg2(myMutex2, std::adopt_lock);
        std::lock_guard<std::mutex> lg1(myMutex1, std::adopt_lock);
        ++g_num;
    }
    // 主线程中创建thread到join的这部分和子线程并行
    myThread.join();

    std::cout << g_num << std::endl;

    return 0;
}
```
