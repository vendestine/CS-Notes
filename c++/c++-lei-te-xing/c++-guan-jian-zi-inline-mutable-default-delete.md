# C++ 关键字 inline mutable default delete

## inline 关键字

### inline关键字作用

在<mark style="background-color:orange;">函数定义中函数返回类型前加上关键字inline</mark>就可以<mark style="background-color:orange;">把函数指定为内联函数</mark>

内联函数的作用，普通函数在调用时需要给函数分配栈空间以供函数执行，压栈等操作会影响成员运行效率，于是C++提供了内联函数将函数体放到需要调用函数的地方，用空间换效率。

简单来说：普通函数调用需要分配新的栈空间，然后执行压栈等操作，而内联函数调用可以继续在当前的函数栈帧里执行，提高了运行效率，<mark style="background-color:orange;">典型的以空间换时间</mark>



总结：<mark style="background-color:orange;">使用 inline 关键字就是一种提高效率，但加大编译后文件大小的方式</mark>，现在随着硬件性能的提高，inline关键字用的越来越少了



### inline关键字的注意事项

(1) <mark style="background-color:orange;">inline关键字只是一个建议，开发者建议编译器将成员函数当做内联函数</mark>，一般<mark style="background-color:orange;">适合搞内联的情况</mark>编译器都会采纳建议

eg: 如果一个函数所需要分配的栈非常大，例如代码量很大的函数，或者是递归函数，他们需要分配的栈空间都很大，这个时候编译器就不会把它当作内联函数；

(2) <mark style="background-color:orange;">关键字 inline 必须与函数定义放在一起才能使函数成为内联</mark>，仅仅将inline放在函数声明前不起任何作用；简单来说：就是inline关键字必须与函数定义放在一起，函数声明加不加inline无所谓；

(3) <mark style="background-color:orange;">直接在类内部实现的函数，相当于函数默认加了inline关键字</mark>



### inline相关代码

```cpp
#include <iostream>

class Test
{
public:
    Test() {}
    ~Test() {}
    inline void func1();
    inline void func2();
    void func3();
    void func4()
    {
        std::cout << "call func4()" << std::endl;
    }
};

inline void Test::func1()
{
    std::cout << "call func1()" << std::endl;
}

void Test::func2()
{
    std::cout << "call func2()" << std::endl;
}

inline void Test::func3()
{
    std::cout << "call func3()" << std::endl;
}

int main()
{
    Test t1;
    t1.func1();   // 函数声明和定义都加inline关键字，建议成内联函数
    t1.func2();   // 函数声明加inline，函数定义不加，不是内联函数
    t1.func3();   // 函数声明不加，函数定义加inline关键字，建议成内联函数
    t1.func4();   // 类内实现函数，建议成内联函数

    // 总结：
    // 1. 关键字 inline 必须与函数定义放在一起才能使函数成为内联，
    // 2. 直接在类内部实现的函数，默认相当于加了inline关键字
    return 0;
}
```



## mutable 关键字

### mutable关键字的作用

<mark style="background-color:orange;">mutable意为可变的，与const相对</mark>，被mutable修饰的成员变量，<mark style="background-color:orange;">永远处于可变的状态；</mark>

<mark style="background-color:orange;">mutable关键字修饰的变量在常函数中，该变量也可以被更改  （常函数中原本是不允许修改成员变量的）</mark>这个关键字在现代 C++中使用情况并不多，<mark style="background-color:orange;">只有在统计函数调用次数这类情况下才推荐使用</mark>



### mutable 关键字的注意事项

(1) mutable不能修饰静态成员变量和常成员变量



### mutable相关代码

```cpp
#include <iostream>

class Test
{
public:
    Test() {}
    ~Test(){}

    void output() const   // mutable关键字修饰的成员变量 可以在常函数里修改
    {
        ++outputCallCount;
        std::cout << "hello world" << std::endl;
    }


    // C++11新特性，直接初始化成员变量 <=> 代替 定义成员 + 构造函数初始化
    mutable unsigned outputCallCount = 0;   // mutable关键字可以修饰普通成员变量
    //mutable static int i = 0;               // mutable关键字不能修饰静态成员变量
    //mutable const int j = 0;                // mutable关键字不能修饰常成员变量

};


int main()
{
    Test t1;
    t1.output();
    t1.output();
    t1.output();

    std::cout << t1.outputCallCount << std::endl;

    return 0;
}
```



## default 关键字

### default 关键字的作用

(1) <mark style="background-color:orange;">便于书写默认构造函数，默认拷贝构造函数，默认的赋值运算符重载函数，默认的析构函数</mark>，default关键字<mark style="background-color:orange;">表示使用的是系统默认提供的代码</mark>，这样可以使代码更加直观，方便；



总结：default关键字还是推荐使用的，在现代C++代码中，如果需要使用一些默认的函数，推荐用default 标记出来

### defalut 关键字注意事项

(1) 现代 C++中，哪怕没有构造函数，也推荐将构造函数用default关键字标记，可以让代码看起来更加直观，方便

(2) 使用default关键字 语法层面就是函数声明 = delete

return\_type fucntion\_name (para list) = default



### default相关代码

```cpp
#include <iostream>

class Test
{
public:
    Test() = default;             // 默认构造函数  
    ~Test() = default;            // 默认析构函数
    Test(const Test& test) = default;   // 默认复制构造函数
    Test& operator=(const Test& test) = default;   // 赋值运算符重载函数   赋值运算符
};

int main()
{
    return 0;
}
```



## delete 关键字

### delete 关键字的作用

(1) <mark style="background-color:orange;">C++会为一个类生成默认构造函数，默认析构函数，默认复制构造函数，默认重载赋值运算符</mark>，在很多情况下，我们并不希望这些默认的函数被生成，为了解决这个问题，在 C++11 以前，只能有将此函数声明为私有函数或是将函数只声明不定义两种方式

于是在<mark style="background-color:orange;">C++11中提供了 delete 关键字，只要在函数最后加上“=delete”就可以明确告诉编译期不要默认生成该函数</mark>



总结：delete关键字还是推荐使用的，在现代 C++代码中，如果不希望一些函数默认生 成，就用 delete 表示，<mark style="background-color:orange;">这个功能还是很有用的，比如在单例模式中</mark>

### delete 关键字注意事项

(1) delete一般不会用使用在析构函数



### delete相关代码

```cpp
#include <iostream>

class Test
{
public:
    Test() = delete;                                // 默认构造函数  
    ~Test() = delete;                               // 默认析构函数
    Test(const Test& test) = delete;                // 默认复制构造函数
    Test& operator=(const Test& test) = delete;     // 默认重载运算符
};

int main()
{
    return 0;
}
```
