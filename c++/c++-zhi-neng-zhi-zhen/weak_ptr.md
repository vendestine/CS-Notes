# weak\_ptr

## 概念用法

weak\_ptr 介绍

(1) 这个智能指针是在 C++11 的时候引入的标准库，它的出现完全是为了弥补 shared\_ptr 天生有缺陷的问题，其实shared\_ptr可以说近乎完美

(2) 只是通过引用计数实现的方式也引来了引用成环的问题，这种问题靠它自己是没办法解决的，所以在C++11的时候将shared\_ptr和weak\_ptr一起引入了标准库，用来解决循环引用的问题。



shared\_ptr 的循环引用问题

两个堆内存中互相有一个shared\_ptr，你指我，我指你，两个堆内存都无法得到释放

解决方案：weak\_ptr 使用较少，就是为了处理shared\_ptr循环引用问题而设计的；将其中一个shared\_ptr改为weak\_ptr就可以解决；



weak\_ptr 的作用原理：

weak\_ptr对象需要绑定到shared\_ptr对象上，作用原理是weak\_ptr 不会改变shared\_ptr 对象的引用计数。只要shared\_ptr对象的引用计数为0，就会 释放内存，weak\_ptr的对象不会影响释放内存的过程





## 相关代码

```cpp
#include <iostream>
#include <vld.h>
#include <memory>


class B;

class A
{
public:
    //std::shared_ptr<B> sharedB;
    std::weak_ptr<B> weakB;    // 修改为weak_ptr解决循环引用
};

class B
{
public:
    std::shared_ptr<A> sharedA;
};


int main()
{
    // weak_ptr不会改变引用计数
    std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    std::cout << sharedI.use_count() << std::endl;

    std::weak_ptr<int> weakI(sharedI);
    std::cout << sharedI.use_count() << std::endl;

    // 循环引用的案例
    std::shared_ptr<A> sharedA = std::make_shared<A>();
    std::shared_ptr<B> sharedB = std::make_shared<B>();

    //sharedA->sharedB = sharedB;
    sharedA->weakB = sharedB;
    sharedB->sharedA = sharedA;


    return 0;
}
```
