# 面向对象，构造函数，析构函数

类介绍：&#x20;

面试的时候经常会听到一个问题，谈一下对面向对象和面向过程的理解。我来说一下我对这两个概念的理解&#x20;

①面向对象和面向过程是一个相对的概念。

② 面向过程是按照计算机的工作逻辑来编码的方式，最典型的面向过程的语言就是c语言了，c语言直接对应汇编，汇编又对应电路。&#x20;

③ 面向对象则是按照人类的思维来编码的一种方式，C++就完全支持面向对象功 能，可以按照人类的思维来处理问题。&#x20;

④ 举个例子，要把大象装冰箱，按照人类的思路自然是分三步，打开冰箱，将大 象装进去，关上冰箱。 要实现这三步，我们就要首先有人，冰箱这两个对象。人有给冰箱发指令的能力，冰箱有能够接受指令并打开或关闭门的能力。

但是从计算机的角度讲，计算机只能定义一个叫做人和冰箱的结构体。人有手 这个部位，冰箱有门这个部位。然后从天而降一个函数，是这个函数让手打开了冰 箱，又是另一个函数让大象进去，再是另一个函数让冰箱门关上。&#x20;

从开发者的角度讲，面向对象显然更利于程序设计。<mark style="background-color:orange;">用面色过程的开发方式， 程序一旦大了，各种从天而降的函数会非常繁琐，一些用纯 c 写的大型程序</mark>，实际上也是模拟了面向对象的方式。 那么，如何用面向过程的 c 语言模拟出面向对象的能力呢？类就诞生了，在类 中可以定义专属于类的函数，让类有了自己的动作。回到那个例子，人的类有了让 冰箱开门的能力，冰箱有了让人打开的能力，不再需要天降神秘力量了。&#x20;

总结：到现在，大家应该可以理解类的重要性了吧，这是面向对象的基石，也可以说是所有现代程序的基石。



构造函数： 类再怎么吹，它也是通过面向过程的机器实现的，类相当于定义了一个新类型，该类型生成在堆或栈上的对象时内存排布和 c 语言相同。但是 c++规定，<mark style="background-color:orange;">C++有在类对象创建时就在对应内存将数据初始化的能力，这就是构造函数</mark>



&#x20;构造函数有以下类型：

1. 普通构造函数：
2. 复制构造函数：用另一个对象来初始化对象对应的内存
3. 移动构造函数：也是用另一个对象来初始化对象
4. 默认构造函数：当类没有任何构造函数时，编译期会为该类生成一个默认的的构造函数，在最普通的类中，默认构造函数什么都没做，对象对应的内存没有被初始化。&#x20;

总结：构造函数就是 C++提供的<mark style="background-color:orange;">必须有的</mark>在对象创建时初始化对象的方法，（默认的什么都不做也是一种初始化的方式）



析构函数： 析构函数介绍：当类对象被销毁时，就会调用析构函数。<mark style="background-color:orange;">栈上对象的销毁时机就是函数栈销毁时，堆上的对象销毁时机就是该堆内存被手动释放时</mark>，如果用new申请的这块堆内存，那调用 delete 销毁这块内存时就会调用析构函数。&#x20;

总结，<mark style="background-color:orange;">当类对象销毁时有一些我们必须手动操作的步骤时，析构函数就派上了用场</mark>。所以，几乎所有的类我们都要写构造函数，析构函数却未必需要。

```cpp
#include <iostream>

class Test
{
public:
    // 类的函数 常用写法1 直接类内部实现
    Test() 
    {
        std::cout << "默认构造函数" << std::endl;
    }
    Test(int i_, int j_, int k_) : i(i_), j(j_), k(new int(k_)) 
    {
        std::cout << "普通构造函数" << std::endl;
    }
    Test(const Test& test) : i(test.i), j(test.j), k(new int(*test.k)) // 深拷贝写法
    {
        std::cout << "拷贝构造函数" << std::endl;
    }

    ~Test()
    {
        delete k;
    }

private:
    int i;
    int j;
    int* k;
};


int main() {
    Test t1;
    Test t2(1, 2, 3);
    
    //Test t3 = t1;    // 这里会报错，因为此时t1.k是nullptr，解引用会报错的
    
    // 拷贝构造函数的两种写法
    Test t4(t2);
    Test t5 = t4;
    return 0;
}
```



this 关键字：&#x20;

this 是什么：&#x20;

① <mark style="background-color:orange;">编译器将 this 解释为指向函数所作用的对象的指针</mark>；C++类的本质就是 C 语言的结构体外加几个类外的 函数，C++最后都要转化为 C 语言来实现，类外的函数就是通过 this 来指向这 个类的。&#x20;

② 当然，这么说并非完全准确，<mark style="background-color:orange;">this其实是一个关键字，只是我们将它当做指针理解罢了</mark>。 this 有很多功能是单纯的指针无法满足的。比如每个类函数的参数根本没有名叫 this 的指针。这不过是编译器赋予的功能罢了

```cpp
#include <iostream>

class Test
{
public:
    // 类的函数 常用写法2，类内声明记得加分号，类外实现记得加命名空间；
    Test(std::string name_, int age_);
    ~Test();
    void output();
private:
    std::string name;
    int age;
};

Test::Test(std::string name_, int age_) : name(name_), age(age_) {}
Test::~Test(){}
void Test::output()
{
    std::cout << "name = " << name << ", age = " << age << std::endl;
}

// 我们发现一个返回值为空，空参数的函数指针 指向了 类的成员函数，这就说明了成员函数里没有this指针
void (Test::*pf)() = &Test::output;



int main() 
{
    Test t1("zhangsan", 20);
    (t1.*pf)();
    return 0;
}
```



> 常成员函数和常对象很多人并不在意，确实都写普通变量也可以；但是在大型程序中，尽量加上const 关键字可以减少很多不必要的错误

常成员函数就是无法修改成员变量的函数。可以理解为将this指针指向对象用const修饰的函数；

常对象就是用 const 修饰的对象，定义好之后就再也不需要更改成员变量的值了。 常对象在大型程序中还是很有意义的;

```cpp
#include <iostream>

class Test
{
public:
    Test(std::string name_, int age_);
    ~Test();
    void output();         // 普通成员函数
    void display() const;  // 常成员函数，无法修改成员变量；
    // 常成员函数 本质是普通成员函数的this加上const修饰，但是参数中没有this，所以直接函数后加个const
    
    std::string name;
    int age;
};

Test::Test(std::string name_, int age_) : name(name_), age(age_) {}
Test::~Test(){}

// 普通成员函数实现
void Test::output()
{
    std::cout << "调用普通成员函数" << std::endl;
    std::cout << "name = " << name << ", age = " << age << std::endl;
}

// 普通成员函数的本质 全局函数 + this指针（Test类指针常量）
void output(Test* const myThis) 
{
    std::cout << "调用普通成员函数" << std::endl;
    std::cout << "name = " << myThis->name << ", age = " << myThis->age << std::endl;
}

// 常成员函数实现
void Test::display() const
{
    std::cout << "调用常成员函数" << std::endl;
    //myThis->age = 100; // 此时不能修改指针指向的对象
}


// 常成员函数的本质 全局函数 + this指针（Test类指针常量指向常量）
void display(const Test* const myThis)
{
    std::cout << "调用常成员函数" << std::endl;
    //myThis->age = 100; // 此时不能修改指针指向的对象
}


int main() 
{
    Test t1("zhangsan", 20);

    t1.output();
    output(&t1);      // 传入对象地址

    t1.display();
    display(&t1);      // 传入对象地址

    const Test t2("lisi", 25);   // 不希望t2对象被修改，所以使用常对象
    //t2.age = 80;                 // t2是常对象，不能修改对象t2

    return 0;
}
```



常成员函数注意事项：&#x20;

因为类的成员函数已经将 this 指针省略了，只能在函数后面加 const 关键字来实现 无法修改类成员变量的功能了（上述代码里也进行了演示）

1. 常函数无法调用了普通函数，否则常函数的这个“常”字还有什么意义

解释：如果一个常函数里可以调用普通函数，那么我们可以调用set函数，去修改对象，那么此时这个常成员函数就没有意义了，所以语法规定，常函数无法调用普通函数

2. 成员函数能写作常成员函数就尽量写作常成员函数，可以减少出错几率
3. 同名的常成员函数和普通成员函数是可以重载的，常量对象会优先调用常成员 函数，普通对象会优先调用普通成员函数

```cpp
#include <iostream>

class Test
{
public:
    Test(std::string name_, int age_);
    ~Test();
    // 两个output函数是重载关系
    void output();         // 普通成员函数
    void output() const;   // 常成员函数
    
    std::string name;
    int age;
};

Test::Test(std::string name_, int age_) : name(name_), age(age_) {}
Test::~Test(){}

void Test::output()
{
    std::cout << "调用普通成员函数output" << std::endl;
    std::cout << "name = " << name << ", age = " << age << std::endl;
}

void Test::output() const
{
    std::cout << "调用常成员函数output" << std::endl;
    std::cout << "name = " << name << ", age = " << age << std::endl;
}


int main() 
{
    Test t1("zhangsan", 20);  //  普通对象
    t1.output();              //  普通对象会优先调用普通成员函数

    const Test t2("lisi", 30); // 常对象
    t2.output();               // 常对象会优先调用常成员

    return 0;
}
```



常对象注意事项：&#x20;

常对象不能调用普通函数，原因和常成员函数不能调用普通函数是一样的



常成员函数和常对象要多用，这真的 是一个非常好的习惯，写大项目可以少出很多bug







