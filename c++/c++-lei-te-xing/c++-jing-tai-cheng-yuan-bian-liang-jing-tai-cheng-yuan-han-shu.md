# C++ 静态成员变量，静态成员函数

## 概念和用法

### 静态成员变量

(1) 静态成员变量，在编译期就已经在静态变量区明确了地址，所以生命周期为程序从开始运行到结束，作用范围为与普通的成员变量相同

(2) 类的静态成员变量因为创建在静态变量区，所以直接属于类，也就是我们可以直接通过类名来调用，当然通过对象调用也可以



### 静态成员函数

(1) <mark style="background-color:orange;">静态成员函数就是为静态成员变量设计的，就是为了维持封装性</mark>；如果静态成员变量是私有的，我们就需要用接口函数来访问保证封装性，为了使用类的方式去访问接口函数，所以设计了静态成员函数；

## 注意事项

(1) <mark style="background-color:orange;">静态成员变量必须在类外进行初始化，否则会报未定义的错误，不能用构造函数进行初始化</mark>。因为静态成员变量在静态变量区，只有一份，而且静态成员变量在编译期就要被创建，成员函数那都是运行期的事情了

<div align="left">

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>



## 相关代码

```cpp
#include <iostream>

class Test
{
public:
    static int getI1() { return i1; }
    int getI2() { return i2; }
    static int getI22() { return i2; }
    static int i1;
private:
    static int i2;
};

int Test::i1 = 10;
int Test::i2 = 20;

int main()
{
    Test t1;
    // 静态成员变量，使用类或者类对象调用都可以
    std::cout << t1.i1 << std::endl;
    std::cout << Test::i1 << std::endl;

    // 静态成员函数，使用类的方式去访问私有静态成员变量，不破坏封装性
    std::cout << t1.getI2() << std::endl;
    std::cout << t1.getI22() << std::endl;
    std::cout << Test::getI22() << std::endl;

    return 0;
}
```
