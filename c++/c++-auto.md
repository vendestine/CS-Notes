# C++ auto

auto 是 C++11 新加入的关键字，就是为了简化一些写法

使用 auto 推断类型确实简单方便，但有个基本要求，就是在使用 auto 时清楚的知道编译器会给 auto 推断出什么类型



可以使用boost库来确定变量的具体类型

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;


int main() {
    auto i = 100;
    std::cout << type_id_with_cvr<decltype(i)>().pretty_name() << std::endl;

    return 0;
}
```



boost使用：

1. 下载boost库，直接官网下载对应平台的zip文件，然后解压
2. windows平台下，打开VS的terminal，首先cd到解压好的boost目录，然后执行bootstrap.bat文件，生成b2.exe文件
3.  执行下面两条命令，编译boost库

    1. b2.exe install --prefix="C:\Boost\x64" --build-type=complete --toolset=msvc-14.2 threading=multi --build-type=complete address-model=64
    2. b2.exe install --prefix="C:\Boost\x86" --build-type=complete --toolset=msvc-14.2 threading=multi --build-type=complete address-model=32

    注意：那个14.2一定要查看一下vs的编译器版本，版本必须相同
4. VS项目里需要使用boost的话，就加入boost库的include目录路径，和lib目录路径；



<mark style="background-color:orange;">auto永远无法推断出引用类型</mark>，要使用引用只能自己加引用符号；

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;

int main() {
    auto i1 = 100;
    auto &i2 = i1;
    auto i3 = i1;

    std::cout << type_id_with_cvr<decltype(i1)>().pretty_name() << std::endl;   // int
    std::cout << type_id_with_cvr<decltype(i2)>().pretty_name() << std::endl;   // int &
    std::cout << type_id_with_cvr<decltype(i3)>().pretty_name() << std::endl;   // int

    return 0;
}
```



<mark style="background-color:orange;">auto 关键字在推断引用的类型时，会直接将引用替换为引用指向的对象</mark>。其实引用一直是这样的，引用不是对象（引用就是别名），任何使用引用的地方都可以直接替换成引用指向的对象

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;


int main() {
    int i = 100;
    const int& refI = i;
    auto i2 = refI;         // 推断引用的类型
    auto &i3 = refI;
    auto &i4 = i;

    std::cout << type_id_with_cvr<decltype(i2)>().pretty_name() << std::endl;   // int
    std::cout << type_id_with_cvr<decltype(i3)>().pretty_name() << std::endl;   // int const &
    std::cout << type_id_with_cvr<decltype(i4)>().pretty_name() << std::endl;   // int &

    return 0;
}
```



auto关键字在推断类型时，<mark style="background-color:orange;">如果没有引用符号</mark>，会<mark style="background-color:orange;">忽略 值类型的const修饰</mark>（本身的const)，而<mark style="background-color:orange;">保留 指向对象的const修饰</mark>，典型的就是指针；

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;


int main() {
    int i = 100;
    const int* const pi = &i;
    auto pi2 = pi;    // 忽略值类型的const，保留指向对象的const

    const int i2 = 100;
    auto i3 = i2;     // 忽略值类型的const

    std::cout << type_id_with_cvr<decltype(pi2)>().pretty_name() << std::endl;   // const int * = int const *
    std::cout << type_id_with_cvr<decltype(i3)>().pretty_name() << std::endl;   // int 

    return 0;
}
```



auto 关键字在推断类型时，<mark style="background-color:orange;">如果有了引用符号</mark>，那么<mark style="background-color:orange;">值类型的const修饰 和 指向对象的const修饰 都会保留</mark>

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;


int main() {
    int i = 100;
    const int* const pi = &i;
    auto &pi2 = pi;    // 都保留

    const int i2 = 100;
    auto &i3 = i2;     // 都保留

    std::cout << type_id_with_cvr<decltype(pi2)>().pretty_name() << std::endl;   // const int * const & = int const * const & 
    std::cout << type_id_with_cvr<decltype(i3)>().pretty_name() << std::endl;   // const int & = int const &

    return 0;
}
```



auto推断不出const，我们可以在前面加上const，这样永远都有 const 的含义

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;


int main() {
    int i = 100;
    const auto i2 = i;

    std::cout << type_id_with_cvr<decltype(i2)>().pretty_name() << std::endl;   // const int = int const
    return 0;
}
```



auto 不会影响编译速度，甚至会加快编译速度。因为编译器在处理 XX a = b 时，当 XX 是传统类型时，编译期需要检查 b 的类型是否可以转化为 XX。当 XX 为 auto 时，编译期 可以按照 b 的类型直接给定变量 a 的类型，所以效率相差不大，甚至反而还有提升。

最重要的一点，就是 auto 不要滥用，对于一些自己不明确的地方不要乱用 auto， 否则很可能出现事与愿违的结果，使用类型应该安全为先。

<mark style="background-color:orange;">auto 主要用在与模板相关的代码中</mark>，一些简单的变量使用模板常常导致可读性 下降，经验不足还会导致安全性问题。
