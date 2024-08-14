# C++ 多继承

## 概念和用法

多继承的概念&#x20;

(1) 一个类同时继承多个类，在内存上，该类对象前面依次为第一个继承的类，第 二个继承的类，依次类推





## 注意事项

(1) 多继承最需要注意的点就是重复继承的问题，这个问题下一个将会详细讲解。&#x20;

(2) 多继承会使整个程序的设计更加复杂，平常不推荐使用。C++语言中用到多继承的 地方主要就是接口模式。相较于C++，java直接取消了多继承的功能，添加了接口

(3) 多继承这个语法虽然在某些情况下使代码写起来更加简洁，但会使程序 更加复杂难懂，一般来说除了借口模式不推荐使用





## 相关代码

```cpp
#include <iostream>

class Base1
{
public:
    Base1(int base1I_) : base1I(base1I_) { std::cout << "Base1(para)" << std::endl; }
    ~Base1() { std::cout << "~Base1()" << std::endl; }
protected:
    int base1I;
};


class Base2
{
public:
    Base2(int base2I_) : base2I(base2I_) { std::cout << "Base2(para)" << std::endl; }
    ~Base2() { std::cout << "~Base2()" << std::endl; }
protected:
    int base2I;
};


class Derived : public Base1, public Base2
{
public:
    Derived(int base1i_, int base2i_, int i_) : Base1(base1i_), Base2(base2i_), i(i_) { std::cout << "Derived(para)" << std::endl; }
    ~Derived() { std::cout << "~Derived()" << std::endl; }
private:
    int i;

};


int main()
{
    Derived d1(10, 20, 30);
    return 0;
}

```



