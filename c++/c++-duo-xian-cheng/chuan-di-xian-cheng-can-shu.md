# 传递线程参数

(1) 传递子线程函数的参数：直接传递即可，注意：传递参数分为三种方式，值传递，引用传递，指针传递。&#x20;

(2) 传递参数注意事项：&#x20;

1. <mark style="background-color:orange;">在使用detach时不要传递指针</mark>，或者说在设置子线程函数时，不要设置指针参数。<mark style="background-color:orange;">因为值传递和引用传递并未直接传递地址，而指针传递却直接传递地址</mark>。所以当使用deatch时，传指针就会导致错误，因为有可能主线程中原对象已经被系统回收，但是子线程函数内部还在使用原对象，所以不要千万不要传指针。&#x20;
2. <mark style="background-color:orange;">在使用detach时不要使用隐式类型转化</mark>，因为很有可能子线程参数还没来的及将参数转化为自己的类型，主线程就已经执行完毕了。&#x20;

(3) 总结：&#x20;

1. 普通对象在传递子线程函数参数时，直接值传递即可。&#x20;
2. 类对象传递引用就可以了，类类型传递引用会首先调用一次复制构造函数生成一 个临时变量，故而导致地址不相同。如果采用值传递，需要两次复制构造函数，开销更大。&#x20;

(4) std::ref 的用法：使用普通的引用传递会调用一次复制构造函数，导致函数无法对引用对象进行修改，于是std::ref 诞生了，它可以使子线程在传递参数时不再调用复制构造函数。



注意事项：

<mark style="background-color:orange;">普通函数传参，和子线程函数传参是有区别的；</mark>

子线程函数传参：值传递是传原对象的拷贝，引用传递和指针传递是传递原对象的地址

子线程函数传参：值传递是传原对象的拷贝，引用传递是传原对象的（拷贝/复制构造后）临时对象的地址，指针传递是传原对象的地址；

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

相关代码：普通函数传参，子线程函数传参

```cpp
#include <iostream>
#include <thread>


void test(int& refI) 
{
    refI = 20;
}

void test2(int i_, const int& refI, const int* pi)
{
    i_ = 30;
}

int main()
{
    int i = 10;
    test(i);
    std::cout << i << std::endl;  // 20

    std::thread myThread(test2, i, i, &i);
    myThread.join();
    std::cout << i << std::endl;  // 20

    return 0;
}
```



相关代码：隐式转换

```cpp
#include <iostream>
#include <thread>


class Test
{
public:
    Test(int i)
    {
        std::cout << std::this_thread::get_id() << std::endl;
    }
};

void test(const Test& str)
{
    std::cout << "child thread id is :" << std::this_thread::get_id() << std::endl;
}

int main()
{
    std::cout << "main thread id is: " << std::this_thread::get_id() << std::endl;

    //传参的时候隐式类型转化
    //int i = 100;
    //std::thread myThread(test, i);
    //myThread.detach();
    
    //直接传对应类型对象
    Test t(100);
    std::thread myThread(test, t);
    myThread.detach();

    return 0;
}
```



相关代码：类对象传递引用会首先调用一次复制构造函数生成一 个临时变量，故而导致地址不相同。如果采用值传递，需要两次复制构造函数， 开销更大

```cpp
#include <iostream>
#include <thread>
#include <Windows.h>

class Test
{
public:
    Test(int i)
    {
        std::cout << std::this_thread::get_id() << std::endl;
    }

    Test(const Test& test)
    {
        std::cout << "Test(const Test& test)" << std::endl;
    }
};

void test(const Test& str)
{
    std::cout << "child thread id is :" << std::this_thread::get_id() << std::endl;
}

int main()
{
    std::cout << "main thread id is: " << std::this_thread::get_id() << std::endl;

    Test t(100);
    std::thread myThread(test, t);
    myThread.join();

    return 0;
}
```

类对象，子线程函数，传值调用两次复制构造，传引用调用一次复制构造；

<div align="left">

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>



因为子线程函数无法通过引用对原对象进行修改，于是std::ref 诞生了，它可以使子线程在传递参数时不再调用复制构造函数；

这里想在子线程函数里通过引用直接修改原对象报错了！

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

相关代码：使用std::ref

```cpp
#include <iostream>
#include <thread>

void test(int i_, int& refI, const int* pi)
{
    refI = 30;
}

int main()
{
    int i = 10;
    std::thread myThread(test, i, std::ref(i), &i);
    myThread.join();
    std::cout << i << std::endl;  // 30

    return 0;
}
```

