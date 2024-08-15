# shared\_ptr

## 概念和用法



### shared\_ptr 的工作原理&#x20;

(1) 我们在动态分配内存时，堆上的内存必须通过指针来寻址，也就是说指针是寻找堆内存的唯一方式

(2) 所以我们可以给堆内存添加一个引用计数，有几个指针指向它，它的引用计数就是几，当引用计数为0是，操作系统会自动释放这块堆内存；



### Shared\_ptr 的常用操作&#x20;

(1) shared\_ptr 的初始化&#x20;

1. 使用new运算符初始化 （一般来说不推荐使用 new 进行初始化，因为 C++标准提供了专门创建 shared\_ptr 的函数“make\_shared”，该函数是经过优化的，效率更高）&#x20;
2. 使用make\_shared函数进行初始化 （推荐使用，和智能指针的类型对应）
3. 使用复制构造函数初始化
4. 使用裸指针初始化（非常不推荐，因为很容易造成内存重复释放和内存泄露的问题）

(2) shared\_ptr 的引用计数

智能指针就是通过引用计数来判断释放堆内存时机的；成员函数use\_count()可以得到shared\_ptr对象指向堆内存的引用计数

(3) 智能指针可以像普通指针那样使用，”share\_ptr”早已对各种操作进行了重载

(4) Shared\_ptr 的常用函数&#x20;

1. unique 函数：判断该shared\_ptr对象是否独占若独占，返回true，否则返回false
2.  reset 函数：&#x20;

    1\)  当reset函数有参数时，改变此shared\_ptr对象指向的内存

    2\) 当reset函数无参数时，将此shared\_ptr对象置空，也就是将对象内存的指针设置为nullptr&#x20;
3. get 函数，强烈不推荐使用，如果一定要使用，那么一定不能delete返回的指针。
4.  swap 函数：交换两个智能指针所指向的内存&#x20;

    1\) std命名空间中全局的swap函数&#x20;

    2\) shared\_ptr 类提供的swap函数

(5) 智能指针指向数组

智能指针访问数组，无法像裸指针一样直接用下标访问数组，所以我们需要利用get()函数拿到裸指针来使用下标访问数组

(6) 用智能指针作为参数传递时直接值传递就可以了

shared\_ptr的大小为固定的8或16字 节（也就是两倍指针的的大小，32 位系统指针为 4 个字节，64 位系统指针为 8 个字节， shared\_ptr 中就两个指针），所以直接值传递就可以了。

(7) 在现代程序中，当想要共享一块堆内存时，优先使用shared\_ptr，可以 极大的减少内存泄露的问题





## 相关代码

```cpp
#include <iostream>
#include <memory>
#include <vld.h>

void myFunc(std::shared_ptr<int> sharedI);


int main()
{
    // 内存泄露举例
    //{
    //    int* pi = new int(100);
    //}

    // shared_ptr初始化1 使用new运算符初始化 不推荐
    //std::shared_ptr<int> sharedI(new int(100));

    // shared_ptr初始化2 使用make_shared函数初始化
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);

    // shared_ptr初始化3 使用复制构造函数初始化
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //std::shared_ptr<int> sharedI2(sharedI);

    // 千万不要用裸指针初始化shared_ptr
    // 极容易导致内存泄露 或 内存的重复释放

    // case1：内存重复释放
    //int* pi = new int(100);
    //std::shared_ptr<int> sharedI(pi);
    //delete pi;

    // case2: 内存泄露


    // shared_ptr的引用计数
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //std::cout << sharedI.use_count() << std::endl;

    //std::shared_ptr<int> sharedI2(sharedI);
    //std::cout << sharedI.use_count() << std::endl;

    //sharedI2.reset();   // reset函数没有参数时表示，该指针放弃对堆内存的控制权，堆内存的引用计数 - 1；
    //std::cout << sharedI.use_count() << std::endl;
    
    // shared_ptr可以像普通指针一样使用
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //std::cout << *sharedI << std::endl;


    // unique函数 独占内存返回true 否则false
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //std::cout << sharedI.unique() << std::endl;

    //std::shared_ptr<int> sharedI2(sharedI);
    //std::cout << sharedI.unique() << std::endl;

    //sharedI2.reset();
    //std::cout << sharedI.unique() << std::endl;


    // reset函数 
    // 当reset函数无参数时，将此shared_ptr对象置空，也就是将对象内存的指针设置为nullptr，原本指向的堆内存引用计数 - 1
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //sharedI.reset();
    //std::cout << sharedI.use_count()<< std::endl;
    //if (sharedI) std::cout << *sharedI << std::endl;

    //// 当reset函数有参数时，改变此shared_ptr对象指向的内存
    //std::shared_ptr<int> sharedI2 = std::make_shared<int>(100);   // 原来的堆内存因为shared_ptr转移，引用计数 - 1，已经置0，被释放了
    //sharedI2.reset(new int(200));                                 // 新的堆内存，转移加了一个指针，引用计数 + 1
    //std::cout << sharedI2.use_count() << std::endl;
    //std::cout << *sharedI2 << std::endl;

    //// reset函数参数不允许是 make_shared出来的堆内存 或者 智能指针
    ////sharedI.reset(sharedI2);                        //不合法
    ////sharedI.reset(std::make_shared<int>(300));      // 不合法

    //// 如果要reset为智能指针，直接利用=
    //sharedI = sharedI2;
    //std::cout << sharedI.use_count() << " " << *sharedI << std::endl;


    // get函数，智能指针就是普通指针的封装  不推荐使用
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //std::cout << *sharedI.get() << std::endl;

    //// 如果一定要使用，千万不能delete，否则会重复释放
    //delete sharedI.get();
    //return 0;



    // swap 函数：交换两个智能指针所指向的内存
    //std::shared_ptr<int> sharedI = std::make_shared<int>(100);
    //std::shared_ptr<int> sharedI2 = std::make_shared<int>(200);

    //// shared_ptr 类提供的swap函数  推荐使用这个
    //sharedI.swap(sharedI2);
    //std::cout << *sharedI << " " << *sharedI2 << std::endl;

    //// std命名空间中全局的swap函数
    //std::swap(sharedI, sharedI2);
    //std::cout << *sharedI << " " << *sharedI2 << std::endl;

    // 智能指针 指向数组
    //std::shared_ptr<int> sharedI(new int[100]());
    ////std::cout << sharedI[10] << std::endl;         // 智能指针访问数组 不能像普通指针一样使用
    //std::cout << sharedI.get()[10] << std::endl;   // 利用get函数得到裸指针，然后访问数组

    // 用智能指针作为参数传递时直接值传递就可以了
    // 智能指针的大小为 两倍指针的的大小
    std::shared_ptr<int> sharedI(new int[100]());
    myFunc(sharedI);
    std::cout << sizeof(sharedI) << std::endl;

    return 0;
}

void myFunc(std::shared_ptr<int> sharedI){}
```
