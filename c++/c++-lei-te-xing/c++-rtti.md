# C++ RTTI

## 概念和用法

RTTI介绍：

(1) RTTI（Run Time Type Identification）即通过运行时类型识别，<mark style="background-color:orange;">程序能够通过基类的指针或引用来检查这些指针或引用所指向的对象的实际派生类</mark>

(2) C++为了支持<mark style="background-color:orange;">多态</mark>，<mark style="background-color:orange;">C++的指针或引用的类型可能与它实际指向对象的类型不相同</mark>， 这时就需要RTTI去判断类的实际类型了，<mark style="background-color:orange;">RTTI是 C++判断指针或引用实际类型的唯一方式（C++在运行阶段判断对象实际类型的唯一方式）</mark>



RTTI 的使用方式：主要关注两个函数&#x20;

(1) typeid 函数：typeid 函数返回的一个叫做type\_info的结构体，该结构体包括了所指向对象的实际信息，其中name()函数就可以返回函数的真实名称。type\_info 结构体其他函数没什么用

(2) dynamic\_cast 函数：C++提供的将父类指针转化为子类指针的函数。





## 注意事项

(1) 当使用 typeid 函数时，父类和子类必须有虚函数（父类有了虚函数，子类自然会有虚函数），否则类型判断会出错







## 相关代码

```cpp
#include <iostream>
#include <string>

class Spear
{
public:
    Spear(std::string name_, std::string icon_) : name(name_), icon(icon_)
    {
        std::cout << "Spear(para)" << std::endl;
    }
    virtual ~Spear()
    {
        std::cout << "~Spear()" << std::endl;
    }

    virtual void openFire() const
    {
        std::cout << "Spear::openFire()" << std::endl;
    }
protected:
    std::string name;
    std::string icon;
};


class FireSpear : public Spear
{
public:
    FireSpear(std::string name_, std::string icon_, int fid_) : Spear(name_, icon_), fid(fid_)
    {
        std::cout << "FireSpear(para)" << std::endl;
    }
    virtual ~FireSpear()
    {
        std::cout << "~FireSpear()" << std::endl;
    }

    virtual void openFire() const override
    {
        std::cout << "FireSpear::openFire()" << std::endl;
    }
protected:
    int fid;
};



class IceSpear : public Spear
{
public:
    IceSpear(std::string name_, std::string icon_, int fid_) : Spear(name_, icon_), Iid(fid_)
    {
        std::cout << "IceSpear(para)" << std::endl;
    }
    virtual ~IceSpear()
    {
        std::cout << "~IceSpear()" << std::endl;
    }

    virtual void openFire() const override
    {
        std::cout << "IceSpear::openFire()" << std::endl;
    }
protected:
    int Iid;
};

int main()
{
    // RTTI使用typeid函数, dynamic_cast函数例子
    Spear* s1 = new FireSpear("fire", "normal", 20);
    std::cout << typeid(*s1).name() << std::endl;

    FireSpear* p1 = dynamic_cast<FireSpear*>(s1);
    if (p1) {
        std::cout << "transfer success!" << std::endl;
    }

    delete s1, p1;

    std::cout << "=================================" << std::endl;

    // RTTI常用操作：判断出Spear指针指向什么类型对象，然后将指针转化为对应类型
    Spear* pSpear = new FireSpear("fire1", "normal1", 30);
    if (std::string(typeid(*pSpear).name()) == "class FireSpear") {
        FireSpear* pFireSpear = dynamic_cast<FireSpear*>(pSpear);
        if (pFireSpear) {
            std::cout << "cast FireSpear success!" << std::endl;
        }
    }

    if (std::string(typeid(*pSpear).name()) == "class IceSpear") {
        IceSpear* pIceSpear = dynamic_cast<IceSpear*>(pSpear);
        if (pIceSpear) {
            std::cout << "cast IceSpear success!" << std::endl;
        }
    }
    delete pSpear;

    return 0;
}
```
