# C++ 移动构造函数与移动赋值运算符



## 概念和用法

对象移动的概念

(1) 对一个体积比较大的类对象进行大量的拷贝操作是非常消耗性能的，因此C++11中加入了“对象移动”的操作&#x20;

(2) 所谓的对象移动，其实就是把该对象占据的内存空间的访问权限转移给另一个对象；比如一块内存原本属于A，在进行“移动语义”后，这块内存就属于B了



移动语义的原理

移动语义为什么可以提高程序运行效率？因为我们的各种操作经常会进行大量的“复制构造”，“赋值运算”操作。这两个操作非常耗费时间。移动构造是直接转移权限，这就快多了。 注意：在进行转移操作后，被转移的对象就不能继续使用了，所以对象移动一般都是对临时对象进行操作（因为临时对象很快就要销毁了）





## 注意事项

(1) 移动语义里的右值引用不能是const的，因为你用右值引用函数参数就算为了让其绑定到一个右值上去的！就是说这个右值引用是一定要变的，但是你一旦加了const就没法改变该右值引用了

(2) 默认移动构造函数和默认移动赋值运算符&#x20;

会默认生成移动构造函数和移动赋值运算符的条件：&#x20;

只有一个类没有定义任何自己版本的拷贝操作（拷贝构造，拷贝赋值运算符），且类的每个非静态成员都可以移动，系统才能为我们合成（可以移动的意思就是可以就行移动构造，移动赋值。所有的基础类型都是可以移动的，有移动语义的类也是可以移动的）





## 相关代码

```cpp
#include <iostream>


class Test
{
public:
    Test() = default;

    // 复制构造函数
    Test(const Test& test)
    {
        if (test.str) {
            str = new char[strlen(test.str) + 1]();
            strcpy_s(str, strlen(test.str) + 1, test.str);
        }
        else {
            str = nullptr;
        }
    }

    // 移动构造函数
    Test(Test&& test)
    {
        if (test.str) {
            str = test.str;
            test.str = nullptr;
        }
        else {
            str = nullptr;
        }
    }

    // 赋值运算符重载
    Test& operator= (const Test& test)
    {
        if (this == &test) {
            return *this;
        }
        if (str) {
            delete[] str;
            str = nullptr;
        }
        if (test.str) {
            str = new char[strlen(test.str) + 1]();
            strcpy_s(str, strlen(test.str) + 1, test.str);
        }
        else {
            str = nullptr;
        }
    }

    // 移动赋值运算符重载
    Test& operator=(Test&& test)
    {
        if (this == &test) {
            return *this;
        }
        if (str) {
            delete[] str;
            str = nullptr;
        }
        if (test.str) {
            str = test.str;
            test.str = nullptr;
        }
        else {
            str = nullptr;
        }
    }

private:
    char* str = nullptr;
};

int main()
{
    // 调用移动构造函数
    std::cout << "=========调用移动构造函数============ " << std::endl;
    Test t1 = Test();

    std::cout << "=========调用移动赋值运算符=========== " << std::endl;
    Test t2;
    // 调用移动赋值运算符
    t2 = Test();
    return 0;
}
```

