# C++ new delete

## 用法



new 关键字是 C++用来动态分配内存的主要方式



new可以分配单个对象的内存，也可以分配数组对象的内存；分配的内存是在堆区；

> 相关术语：
>
> new分配单个对象的内存 <=> new创建单个对象 <=> new单个对象
>
> new分配数组对象的内存 <=> new创建数组对象 <=> new数组对象
>
>
>
> new的时候 语法层面有三种写法,(..,), (), 没有()；分别对应有参初始化，无参初始化，不初始化；



new分配单个对象的内存：

1. 对象是普通变量，可以分配对应的内存
   1. `(...)` 有参初始化，允许；
   2. `()`无参初始化，允许，初始化为0；
   3. 没有`()` 不初始化，允许，分配的内存未定义；
2. 对象是类对象，会调用构造函数，如果没有对应的构造函数，就会报错
   1. `(...)` 有参初始化，允许，找到对应构造函数初始化，没有找到报错
   2. `()` 无参初始化，允许，调用默认构造函数初始化，没有找到报错
   3. 没有`()` 不初始化，允许，调用默认构造函数初始化，没有找到报错



new分配数组对象的内存：

1. new普通变量数组，可以使用`()`将所有对象全部初始化为0
   1. `(...)` 有参初始化，<mark style="background-color:red;">不允许</mark>；会报错
   2. `()`无参初始化，允许，数组中对象全部初始化为0；
   3. 没有`()` 不初始化，允许，分配的内存未定义；
2. new类对象数组，有没有`()`都一样，均使用默认构造函数，如果没有默认构造函数就 会报错
   1. `(...)` 有参初始化，<mark style="background-color:red;">不允许</mark>；会报错
   2. `()` 无参初始化，允许，调用默认构造函数初始化，没有找到报错
   3. 没有`()` 不初始化，允许，调用默认构造函数初始化，没有找到报错



代码：

```cpp
#include <iostream>
#include <string>


class Test
{
public:
    Test() {}
};

class TestA
{
public:
    TestA(int i_) : i(i_) {}

private:
    int i;
};

int main() {
    // 1. new可以在堆上分配 单个对象 的内存  <=> new可以在堆上创建 单个对象   <=> new单个对象

    // 以下对象 指 分配的对象/创建的对象/new的对象

    // 1.1 对象是普通变量，分配对应的内存
    
    int *pi = new int(10);    // 堆上分配int对象的内存，有参初始化
    std::cout << *pi << std::endl;
   
    int* pk = new int();     // 堆上分配int对象的内存，()无参初始化为0;
    std::cout << *pk << std::endl;
    
    int *pj = new int;       // 堆上分配int对象的内存，不初始化，分配内存未定义
    std::cout << *pj << std::endl;
    
    delete pi;   
    delete pj;
    delete pk;

    // 1.2 对象是类对象，会调用对应的构造函数，如果没有对应的构造函数，就会报错
    
    std::string *pString1 = new std::string("hello world");  // 找到对应的构造函数初始化
    std::cout << *pString1 << std::endl;

    std::string *pString2 = new std::string();              // 调用默认构造函数初始化
    std::cout << *pString2 << std::endl;
    
    std::string *pString3 = new std::string;                // 调用默认构造函数初始化
    std::cout << *pString3 << std::endl;

    // 这里演示 new单个类对象，找不到对应构造函数报错
    //Test* t1 = new Test(10);     // 找不到对应构造函数报错
    
    delete pString1;
    delete pString2;
    delete pString3;


    // 2. new可以在堆上分配 数组对象 的内存 <=> new可以在堆上创建 数组对象 <=> new数组对象

    // 2.1 new 普通变量 数组   可以使用（）将所有对象全部初始化为 0    => 只有()初始化合法

    int *p1 = new int[100]();           // 分配数组对象内存，无参初始化，数组中所有对象全部初始化为0 
    std::cout << p1[20] << std::endl;

    int *p2 = new int[100];             // 分配数组对象内存，不初始化，分配的内存未定义
    std::cout << p2[20] << std::endl;   

    //int* p3 = new int[100](10);        // 分配数组对象内存，有参初始化，报错，语法规定不允许
    //std::cout << p2[20] << std::endl;   

    delete[] p1;
    delete[] p2;
    //delete[] p3;

    // 2.2 对于 类对象 数组   有没有“（）”都一样，均使用默认构造函数，如果没有默认构造函数就会报错

    std::string *pString4 = new std::string[100]();                // 分配数组对象内存，数组中所有对象 使用默认构造函数初始化
    std::cout << pString4[20] << std::endl;

    std::string* pString5 = new std::string[100];                  // 分配数组对象内存，数组中所有对象 使用默认构造函数初始化
    std::cout << pString5[20] << std::endl;

    //std::string* pString6 = new std::string[100]("hello world");   // 分配数组对象内存，有参初始化，报错，语法规定不允许 
    //std::cout << pString6[20] << std::endl;

    // 这里演示 new类对象数组，找不到默认构造函数报错
    //TestA* t2 = new TestA[100];
    
    delete[] pString4;
    delete[] pString5;
    //delete[] pStirng6;

    return 0;
}
```

<div align="left">

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

</div>





上面代码 注释的地方都是之前提到的的问题

内存未定义，没有找到对应的构造函数，new数组对象不允许有参初始化

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption><p>报错合集</p></figcaption></figure>





总结：

1. new单个对象，语法层面上有，有参/无参/无 初始化都可以；但是new数组对象上，语法层面上不允许有参初始化；
2. new 单个类对象 和 new 类对象数组时，就是要找对应的构造函数，没有找到就会报错



