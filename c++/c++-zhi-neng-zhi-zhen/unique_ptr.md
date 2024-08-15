# unique\_ptr



## 概念和用法

uniqe\_ptr介绍

独占式智能指针，在使用智能指针时，我们一般优先考虑独占式智能指针，因为消耗更小；如果发现内存需要共享，那么再去使用“shared\_ptr”



unique\_ptr 的初始化：和shared\_ptr 完全类似&#x20;

(1) 使用new运算符进行初始化&#x20;

(2) 使用make\_unique 函数进行初始化

(3) 不能使用复制构造函数初始化！



unique\_ptr 的常用操作&#x20;

(1) unque\_ptr 禁止复制构造函数，也禁止赋值运算符的重载。否则独占便毫无意义

(2) unqiue\_ptr 允许移动构造，移动赋值。移动语义代表之前的对象已经失去了意义， 移动操作自然不影响独占的特性&#x20;

(3) reset 函数：&#x20;

1. 不带参数的情况下：释放智能指针的对象，并将智能指针置空
2. 带参数的情况下：释放智能指针的对象，并将智能指针指向新的对象。 代码演示：

(4) 将unque\_ptr 的对象转化为shared\_ptr对象

当unique\_ptr 的对象为一个右值时，就可以将该对象转化为shared\_ptr的对象。 这个使用的并不多，需要将独占式指针转化为共享式指针常常是因为先前设计失误。 注意：shared\_ptr 对象无法转化为unique\_ptr对象





## 相关代码

```cpp
#include <iostream>
#include <vld.h>
#include <memory>

int main()
{
    // unique_ptr初始化    使用new初始化
    std::unique_ptr<int> uniqueA(new int(100));
    std::cout << *uniqueA << std::endl;

    // unique_ptr初始化    使用make_unique初始化
    std::unique_ptr<int> uniqueB = std::make_unique<int>(100);
    std::cout << *uniqueB << std::endl;


    // 禁止复制构造函数 和 =运算符
    //std::unique_ptr<int> uniqueC(uniqueB);
    //uniqueB = uniqueA;

    // 允许移动构造 和移动赋值运算符
    std::unique_ptr<int> unique1 = std::make_unique<int>(100);
    std::unique_ptr<int> unique2(std::move(unique1));   // 使用移动构造后 unique1就置空了，此时堆内存还是由unique_ptr独占的(unique2独占）
    std::cout << *unique2 << std::endl;
    std::unique_ptr<int> unique3 = std::make_unique<int>(200);
    unique2 = std::move(unique3);
    std::cout << *unique2 << std::endl;


    // reset函数
    // 不带参数的情况下：释放智能指针的对象，并将智能指针置空
    std::unique_ptr<int> uniqueI = std::make_unique<int>(100);
    uniqueI.reset();
    if (uniqueI) std::cout << *uniqueI << std::endl;

    // 带参数的情况下：释放智能指针的对象，并将智能指针指向新的对象
    std::unique_ptr<int> uniqueI2 = std::make_unique<int>(200);
    uniqueI2.reset(new int(300));
    if (uniqueI2) std::cout << *uniqueI2 << std::endl;
    

    // unique_ptr 转化为 shared_ptr  unique_ptr对象需要先转化为右值
    std::unique_ptr<int> u1 = std::make_unique<int>(100);
    std::shared_ptr<int> s1(std::move(u1));

    return 0;
}
```
