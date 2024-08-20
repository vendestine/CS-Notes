# C++ 模板的重载，全特化和偏特化

模板的重载:&#x20;

(1) 函数模板是可以重载的（类模板不能被重载），通过重载可以应对更加复杂的情况。 比如在处理char\*_和string对象时，虽然都可以代表字符串，但char_在复制时直接 拷贝内存效率明显更高，string就不得不依次调用构造函数了。所以在一些比较追求效率的程序中对不同的类型进行不同的处理还是非常有意义的。 其实函数模板的重载和普通函数的重载没有什么区别。&#x20;



模板的特化:

(1) 模板特化的意义：函数模板可以重载以应对更加精细的情况。类模板不能重载，但 可以特化来实现类似的功能。&#x20;

(2) 模板的特化也分为两种，全特化和偏特化。

模板的全特化：就是指模板的实参列表与与相应的模板参数列表一一对应；简单来说，类定义的时候泛型参数全部指定；

模板的偏特化：偏特化就是介于普通模板和全特化之间，只存在部分类型明确化， 而非将模板唯一化；简单来说，类定义的时候泛型参数指定一部分；&#x20;

(4) 其实对于函数模板来说，特化与重载可以理解为一个东西。 总结：函数模板的重载，类模板的特化。还是比较重要的知识点，应当掌握，在一些 比较复杂的程序中，模板重载与特化是经常使用的。



注意事项：

(1) 函数模板需要特殊处理，使用重载；类模板需要特殊处理时，使用特化



代码示例：

```cpp
#include <iostream>


// 函数模板重载
template<typename T>
void test(const T& param)
{
    std::cout << "void test(const T& param)" << std::endl;
}

template<typename T>
void test(T* param)
{
    std::cout << "void test(T* param)" << std::endl;
}

void test(double param)
{
    std::cout << "void test(double param)" << std::endl;
}


// 类模板特化
template<typename T1, typename T2>
class Test
{
public:
    Test() { std::cout << "common template" << std::endl; }
};

template<typename T1, typename T2>
class Test<T1*, T2*>
{
public:
    Test() { std::cout << "point semi-special" << std::endl; }
};


template<typename T2>
class Test<int, T2>
{
public:
    Test() { std::cout << "int semi-special" << std::endl; };
};


template<>
class Test<int, int>
{
public:
    Test() { std::cout << "int, int complete special" << std::endl; }
};


int main()
{
    test(100);

    int i = 200;
    test(&i);
    test(2.2);

    std::cout << "===================" << std::endl;

    Test<double, double> test;
    Test<int*, int*> test1;
    Test<int, double> test2;
    Test<int, int> test3;
    return 0;
}
```
