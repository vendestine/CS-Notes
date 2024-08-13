# C++ 重载运算符

## 重载运算符 作用

(1) 很多时候我们想让类对象也能像基础类型的对象一样进行作基础操作，比如“+”， “-”，“\*”，“\”，也可以使用某些运算符“=”，“()”，“\[]”,“<<”，“>>”。 但是一般的类即使编译器可以识别这些运算符，类对象也无法对这些运算符做出应对，我们<mark style="background-color:orange;">必须对类对象定义处理这些运算符的方式</mark>

(2) C++提供了定义这些行为的方式，就是<mark style="background-color:orange;">“operator 运算符”来定义运算符的行为， operator 是一个关键字</mark>，告诉编译器我要重载运算符了





## 重载运算符 注意事项

(1) 我们只能重载 C++已有的运算符，eg: 无法将`**`这个运算符定义为指数的形式， 因为 C++根本没有`**`这个运算符

(2) C++重载运算符不能改变运算符的元数，“元数”这个概念就是指一个运算符对应的对象数量，比如“+”必须为“a + b”，也就是说“+”必须有两个对象，那么“+”就是二元运算符。比如“++”运算符，必须写为“a++”，也就是一元运算符；

(3) 重载运算符的技巧：如果需要调用原对象，修改原对象，运算结果为左值，那么就返回引用；如果只是访问对象，运算结果为右值，那么就返回值；

(4) 重载运算符有两种主要实现方式：

1. 友元重载，直接定义全局的重载运算符函数，然后再在类中声明为友元函数
2. 成员函数重载，使用类的成员函数重载运算符，第一个参数需要使用this，所以<< >>无法使用此方式重载

(5) =运算符会默认进行重载，如果不需要可以用delete 关键字进行修饰。&#x20;



## 重载运算符 相关代码

运算符有很多，我们在重载运算符的时候一般按照以下框架去写

```
一元运算符重载: 
自增，自减 ++ -- 
下标 [] 
调用 () 
输入输出 <<，>>
 
二元运算符重载 
基本运算 +，-，*，/  
赋值 = 
比较 >，<，== 

三元运算符?:，不能重载 

类类型转化运算符：
operator 类型
 
特殊的运算符：new，delete，new[]，delete[] 
```

```cpp
#include <iostream>
#include <vector>


class MyInt
{   
    // 重载 << >> 必须使用友元，因为第一个参数无法用this访问
    friend std::ostream& operator<<(std::ostream& os, const MyInt& t);
    friend std::istream& operator>>(std::istream& is, MyInt& t);
public:
    MyInt() : val(0) {}
    MyInt(int val_) : val(val_) {}

    // 重载前缀++
    MyInt& operator++()
    {
        ++val;
        return *this;
    }

    // 重载后缀++
    MyInt operator++(int)
    {
        MyInt tmp = *this;
        val++;
        return tmp;
    }

    // 重载前缀--
    MyInt& operator--()
    {
        --val;
        return *this;
    }

    // 重载后缀--
    MyInt operator--(int)
    {
        MyInt tmp = *this;
        val--;
        return tmp;
    }

    // 重载[]
    int operator[](unsigned i)const
    {
        return a[i];
    }

    // 重载()
    void operator()()const
    {
        std::cout << "call function()" << std::endl;
    }


    // 重载+   -*/都差不多就不写了
    MyInt operator+(const MyInt& t)
    {
        val += t.val;
        return *this;
    }

    // 重载=   注意防止自赋值 然后返回原对象（返回引用） 
    MyInt& operator=(const MyInt& t)
    {
        if (this == &t) return *this;
        val = t.val;
        return *this;
    }

    // 重载 <   >,>=，==这些都差不多
    bool operator<(const MyInt& t)const
    {
        return val < t.val;
    }

    int val;
    std::vector<int> a{ 1, 2, 3, 4, 5 };
};


std::ostream& operator<<(std::ostream& os, const MyInt& t)
{
    os << t.val;
    return os;
}

std::istream& operator>>(std::istream& is, MyInt& t)
{
    is >> t.val;
    return is;
}

int main()
{
    std::cout << "测试 ++" << std::endl;
    MyInt t1(10);
    ++t1;
    std::cout << "expected: t1 = 11 " << "now: t1 = " << t1 << std::endl;
    MyInt t2 = t1++;
    std::cout << "expected: t1 = 12, t2 = 11 " << "now: t1 = " << t1 << " now t2 = " << t2 << std::endl;

    std::cout << "探索 前缀++和后缀++ 区别" << std::endl;
    int a = 3;
    int b = ++(++a);   // 验证 前缀++后的运算结果是左值，可以继续调用，所以重载需要返回引用
    //int c = (a++)++;   // 验证 后缀++后的运算结果是右值，不能继续调用，所以重载是返回值
    int c = a++;
    std::cout << a << " " << b << " " << c << std::endl;
    

    // 测试 --
    std::cout << "测试 --" << std::endl;
    --t1;
    t1--;
    std::cout << "expected: t1 = 10 " << "now: t1 = " << t1 << std::endl;

    // 测试[]
    std::cout << "测试 []" << std::endl;
    std::cout << "expected: t1[1] = 2 " << "now: t1[1] = " << t1[1] << std::endl;

    // 测试()
    std::cout << "测试 ()" << std::endl;
    t1();


    // 测试 << >>
    std::cout << "测试 << >>" << std::endl;
    MyInt t3;
    std::cin >> t3;
    std::cout << t3 << std::endl;


    // 测试 +
    std::cout << "测试 +" << std::endl;
    MyInt t4 = t1 + t2;
    std::cout << t4 << std::endl;

    // 测试=
    std::cout << "测试 =" << std::endl;
    MyInt t5;
    std::cout << t5 << std::endl;
    t5 = t4;
    std::cout << t5 << std::endl;

    // 测试<
    std::cout << "测试 <" << std::endl;
    MyInt t6(10), t7(12);
    std::cout << (t6 < t7) << std::endl;
    //return 0;
}

```



## Reference

1. [https://www.bilibili.com/video/BV13T411x7bj/?spm\_id\_from=333.337.search-card.all.click\&vd\_source=cb02f779bd17a3aad9801e0c4464dfc9](https://www.bilibili.com/video/BV13T411x7bj/?spm\_id\_from=333.337.search-card.all.click\&vd\_source=cb02f779bd17a3aad9801e0c4464dfc9)&#x20;
2. [https://blog.shipengx.com/archives/5ac252d6.html](https://blog.shipengx.com/archives/5ac252d6.html)
3.
