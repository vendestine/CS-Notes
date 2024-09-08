# 完美转发

C++完美转发的定义：它描述的其实就是<mark style="background-color:orange;">一个参数传递的过程，能够将传递到一个函数的参数，再通过该函数原封不动的传递给另一个函数</mark>（这里的原封不动不单是指参数的值，更包括参数的类型，参数的限定符）&#x20;



万能引用存在一定的问题，我们发现以前的传递参数的方法都无法在万能引用中解决完美转发的问题

首先要了解int&& refI，int&&是右值引用类型，refI是右值引用，但是refI本身是个左值；

问题：按照下述方法传递，无论func1传入左值还是右值，最后传给func2都是左值，所以当func1传入右值参数的时候，就会出现问题；

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;

template<typename T>
void func2(T&& parm);

template<typename T>
void func1(T&& parm)
{
    // 无论func1传入左值还是右值，这里传给func2的都是左值！
    func2(parm);  // parm此时是右值引用，右值引用本身是左值，传递的还是左值
}

template<typename T>
void func2(T&& parm) 
{
    std::cout << type_id_with_cvr<decltype(parm)>().pretty_name() << std::endl;  // 最后参数的类型判断是左值引用类型 int&，明显是错误的
}

int main()
{
    int i = 10;
    func1(std::move(i));   // 传递右值
    return 0;
}
```

尝试解决上述问题，我们把func2(parm)改成func2(std::move(parm))；

新的问题：使用std::move()后，func1无论传入左值还是右值，这里都变成右值了，所以最后传给func2的都是右值，当func1传入左值参数后，就会出现问题；

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;

template<typename T>
void func2(T&& parm);

template<typename T>
void func1(T&& parm)
{
    // 尝试这样解决上述问题，但是这种方式无论func1传入的是左值还是右值，这里都变成右值了
    func2(std::move(parm));    // 传递右值
}

template<typename T>
void func2(T&& parm) 
{
    std::cout << type_id_with_cvr<decltype(parm)>().pretty_name() << std::endl;  // 最后参数的类型判断是右值引用类型 int&&，明显是错误的
}

int main()
{
    int i = 10;
    func1(i);   // 传递左值
    return 0;
}
```





于是C++提供了forward模板来解决完美转发的问题，forward模板可以使参数推断出它原来的类型，实现了完美转发。

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;

template<typename T>
void func2(T&& parm);

template<typename T>
void func1(T&& parm)
{
    // 完美转发
    func2(std::forward<T>(parm));
}

template<typename T>
void func2(T&& parm) 
{
    std::cout << type_id_with_cvr<decltype(parm)>().pretty_name() << std::endl;  
}

int main()
{
    int i = 10;
    func1(i);               // 传递左值
    func1(std::move(i));    // 传递右值
    return 0;
}
```



总结：完美转发就是一个专门配合万能引用的知识点，专门用来在使用万能引用的地方原封不动的传递参数。
