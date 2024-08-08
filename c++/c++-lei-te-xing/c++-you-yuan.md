# C++ 友元

## 友元 作用

友元是针对类来说的，<mark style="background-color:orange;">友元可以让另一个类或函数访问私有成员</mark>



总结：友元平常并不推荐使用，只要可以用友元写出必须用友元的重载运算符就可以了



## 友元 注意事项

(1) <mark style="background-color:orange;">友元会破坏封装性</mark>一般不推荐使用，所带来的方便写几个<mark style="background-color:orange;">接口函数</mark>就解决了

(2) <mark style="background-color:orange;">某些运算符的重载必须用到友元的功能</mark>，这才是友元的真正用途





## 友元 相关代码

友元基本使用

```cpp
#include <iostream>

class Test
{
    friend class Test2;                         // 声明Test2为友元类
    friend void output(const Test& test);       // 声明output函数为友元函数

public:
    Test() = default;
    ~Test() = default;
private:
    std::string name = "lisi";
    int age = 100;
};


// 另一个类访问私有成员
class Test2
{
public:
    Test2() {};
    ~Test2() {};

    void output(const Test& test) const  // 为了在Test2类里访问Test类的私有成员，在Test类里声明Test2为友元类
    {
        std::cout << "name = " << test.name << ", age = " << test.age << std::endl;
    }
};

// 另一个函数访问私有成员
void output(const Test& test)          // 为了在output函数里访问Test的私有成员，在Test类里声明output函数为友元函数
{
    std::cout << "name = " << test.name << ", age = " << test.age << std::endl;
}

int main()
{
    Test t1;
    Test2 t2;
    t2.output(t1);
    output(t1);
    return 0;
}
```



利用公共接口代替友元

```cpp
#include <iostream>

class Test
{
public:
    Test() = default;
    ~Test() = default;

    // 要想其他的类或函数 访问私有成员，友元会破坏封装性
    // 最好的方式是直接多写一些公共接口就行了，也可以达到一样的效果，而且不破坏封装性
    std::string getName() const{ return name; }
    int getAge() const { return age; }

private:
    std::string name = "lisi";
    int age = 100;
};


// 另一个类访问私有成员
class Test2
{
public:
    Test2() {};
    ~Test2() {};

    void output(const Test& test) const
    {   // 使用公共接口访问
        std::cout << "name = " << test.getName() << ", age = " << test.getAge() << std::endl;
    }
};

// 另一个函数访问私有成员
void output(const Test& test)          
{
    // 使用公共接口访问
    std::cout << "name = " << test.getName() << ", age = " << test.getAge() << std::endl;
}

int main()
{
    Test t1;
    Test2 t2;
    t2.output(t1);
    output(t1);
    return 0;
}
```
