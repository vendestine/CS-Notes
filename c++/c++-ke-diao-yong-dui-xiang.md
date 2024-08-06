# C++ 可调用对象

如果一个对象可以使用调用运算符“()”，()里面可以放参数，这个对象就是可调用对象



### 可调用对象分类

函数：函数自然可以调用()运算符，是最典型的可调用对象



仿函数：具有operator()函数的类对象，此时类对象可以当做函数使用，因此称为仿函数

```cpp
#include <iostream>

class Test    // 有operator()函数
{
public:
    void operator()(int i)
    {
        std::cout << i << std::endl;
        std::cout << "hello world" << std::endl;
    }
};

int main() 
{
    Test t;   // t此时就是一个仿函数
    t(20);
    return 0;
}
```



lambda 表达式：<mark style="background-color:orange;">就是匿名函数</mark>，普通的函数在使用前需要找个地方将这个函数定义，于是 C++提供了 lambda 表达式，<mark style="background-color:orange;">需要函数时直接在需要的地方写一个 lambda 表达式，省去了定义函数的过 程，增加开发效率</mark>

```cpp
#include <iostream>

int main() 
{
    [] {
        std::cout << "hello world" << std::endl;
    }();
    return 0;
}
```

> 注意：lambda 表达式很重要，现代 C++程序中，lambda 表达式是大量使用的。&#x20;



lambda 表达式的格式：最少是“\[] {}”，完整的格式为“\[] () ->ret {}”。&#x20;

lambda 各个组件介绍

1.  \[]代表捕获列表：表示 lambda 表达式可以访问前文的哪些变量。&#x20;

    (1) \[]表示不捕获任何变量。&#x20;

    (2) \[=]：表示按值捕获所有变量。&#x20;

    (3) \[&]：表示按照引用捕获所有变量。&#x20;

    \=，&也可以混合使用，比如&#x20;

    (4) \[=, \&i]：表示变量 i 用引用传递，除 i 的所有变量用值传递。&#x20;

    (5) \[&, i]：表示变量 i 用值传递，除 i 的所有变量用引用传递。&#x20;

    当然，也可以捕获单独的变量&#x20;

    (6) \[i]：表示以值传递的形式捕获 i&#x20;

    (7) \[\&i]：表示以引用传递的方式捕获 i
2. ()代表 lambda 表达式的参数，函数有参数，lambda 自然也有。
3. \->ret 表示指定 lambda 的返回值，如果不指定，lambda 表达式也会推断出一个返回值的。
4. {}就是函数体了，和普通函数的函数体功能完全相同



### 可调用对象的常见用法：

1. 可调用对象给另一个函数作为参数

```cpp
#include <iostream>

void test(int i)
{   
    std::cout << i << std::endl;
    std::cout << "hello world" << std::endl;
}

using pf_type = void(*)(int);   // 函数指针

void myFunc(pf_type pf, int i)  // 可调用对象作为函数的参数
{
    pf(i);
}

int main() 
{
    myFunc(test, 200);
    return 0;
}
```



