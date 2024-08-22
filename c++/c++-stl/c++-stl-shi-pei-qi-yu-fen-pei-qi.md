# C++ STL适配器与分配器

什么是容器适配器：

“适配器是使一种事物的行为类似于另外一种事物行为的一种机制”。 适配器对容器进行包装，使其表现出另外一种行为。例如：stack 实现了栈的功能，内部默认使用deque容器来存储数据。



STL 的适配器有哪些：

标准库提供了三种顺序容器适配器，没有关联型容器的适配器。分别是queue（队列），priority\_queue（优先级队列），stack（栈）



适配器的使用：&#x20;

(1) 要使用适配器，首先需要引入对应的头文件&#x20;

1. 要使用stack，需要#include \<stack>
2. 要使用queue或priority\_queue， 需要#include\<queue>

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

分配器：

在分配动态内存时，直接使用new，delete容易产生内存碎片化 的问题，不同的分配器有不同的分配内存的方法，可以大幅提高程序对堆内存的使用效率， 我们直接使用默认的分配器就可以了



stack，queue，priority\_queue覆盖默认容器类型

```cpp
#include <iostream>
#include <stack>
#include <queue>
#include <vector>
#include <list>
#include <deque>

int main()
{
    // stack适配器，可以由vector，deque，list封装而成
    std::stack<int, std::vector<int>> stk1;
    std::stack<int, std::deque<int>> stk2;
    std::stack<int, std::list<int>> stk3;

    // queue适配器，要求push_front()，所以可以由dequeue，list封装而成
    //std::queue<int, std::vector<int>> queue1;
    std::queue<int, std::deque<int>> queue2;
    std::queue<int, std::list<int>> queue3;

    // priority_queue适配器，要求提供随机访问，所以可以有vector，deque封装而成
    std::priority_queue<int, std::vector<int>> heap1;
    std::priority_queue<int, std::deque<int>> heap2;
    //std::priority_queue<int, std::list<int>> heap3;

    return 0;
}
```
