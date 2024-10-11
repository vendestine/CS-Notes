# C++ 虚继承

## 概念和用法



虚继承概念：虚继承就是为了避免多重继承时产生的二义性问题

<div align="left">

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>

虚继承的实现原理：

(1) 使用了虚继承的类会有一个虚继承表，表中存放了父类所有成员变量相对于类的偏移地址&#x20;

(2) 按照刚才的代码，B1，B2类同时有一个虚继承表，当C类同时继承B1和B2类时，每继承一个就会用虚继承表进行比对，发现该变量在虚继承表中偏移地址相同，就只会继承一份



## 相关代码

```cpp
// 虚继承

#include <iostream>

class TrueBase
{
public:
    int id = 100;
};


class Base1: virtual public TrueBase
{
public:
    Base1(int base1I_) : base1I(base1I_) { std::cout << "Base1(para)" << std::endl; }
    ~Base1() { std::cout << "~Base1()" << std::endl; }

    int base1I;
    int i = 1;
};


class Base2 : virtual public TrueBase
{
public:
    Base2(int base2I_) : base2I(base2I_) { std::cout << "Base2(para)" << std::endl; }
    ~Base2() { std::cout << "~Base2()" << std::endl; }

    int base2I;
    int i = 2;
};


class Derived : public Base1, public Base2
{
public:
    Derived(int base1i_, int base2i_) : Base1(base1i_), Base2(base2i_) { std::cout << "Derived(para)" << std::endl; }
    ~Derived() { std::cout << "~Derived()" << std::endl; }

    int i = 3;
};


int main()
{
    Derived d1(10, 20);
    // 子类对象可以访问父类的成员，因为子类对象的内存开始部分有父类对象的内存
    std::cout << d1.base1I << " " << d1.base2I << std::endl;
    // 子类对象和父类对象如果有同名成员变量，不加类作用域默认访问的是子类的成员变量，要访问父类的成员变量，需要加父类的作用域
    std::cout << d1.Base1::i << " " << d1.Base2::i << " " << d1.i << std::endl;

    // 如果不使用虚继承，子类对象同时继承了Base1和Base2的id成员变量，访问时不明确
    // 使用了虚继承后，子类对象只继承了一份id成员变量，访问明确
    std::cout << d1.Base1::id << " " << d1.Base2::id << " " << d1.id << std::endl;

    return 0;
}
```
