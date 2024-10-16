# 万能引用

万能引用的概念：&#x20;

(1) C++11 除了带来了右值引用，还带来了万能引用，也就是既能当做左值，又能当做右值的引用。&#x20;

注意：万能引用是既可以被编译期处理为左值引用，又可以被编译期处理为右值引用。不是既是左值引用又是右值引用，不违背C++一个引用不是左值引用，就是右值引 用的基本说法，<mark style="background-color:orange;">万能引用会在编译期被当做左值引用或右值引用处理。</mark>



万能引用的格式：万能引用的格式有两种&#x20;

(1) 模板型：&#x20;

template\<typename T>&#x20;

void func(T&& parm)&#x20;

<mark style="background-color:orange;">这个T&& 就是万能引用类型</mark>

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;

template<typename T>
void test(T&& elem) {
    std::cout << "elem type is: " << type_id_with_cvr<decltype(elem)>().pretty_name() << std::endl;
    std::cout << "T type is: " << type_id_with_cvr<T>().pretty_name() << std::endl;
}

int main()
{
    int i = 10;
    test(i);
    std::cout << "==================" << std::endl;
    test(std::move(i));
    return 0;
}
```

<div align="left">

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>

注意：只有T&& 是万能引用类型，以下的写法均不是万能引用：&#x20;

```cpp
const T&& parm   // 这就是普通的右值引用
vector && parm   // 这也是右值引用

// 注意：此时T&& 不是万能引用，因为T影响的是MyVector的类型
template<typename T> 
class MyVector
{
public:
    void push_back(T&& elem);
}

// 只有这样写才是万能引用
// 此时T2&&的类型完全独立于MyVector类了，每调用一次push_back函数，都要推断T2的类型
template <typename T> 
class MyVector 
{
public: 
    template<typename T2> 
    void push_back(T2&& elem);
}
```



(2) auto 型：&#x20;

auto && var = var2；&#x20;

<mark style="background-color:orange;">这个auto&& 就是万能引用类型</mark>

```cpp
#include <iostream>
#include <boost/type_index.hpp>

using boost::typeindex::type_id_with_cvr;

int main()
{
    int i = 10;
    auto&& elem = i;
    std::cout << type_id_with_cvr<decltype(elem)>().pretty_name() << std::endl; //int &

    std::cout << "=============================" << std::endl;

    auto&& elem1 = std::move(i);
    std::cout << type_id_with_cvr<decltype(elem1)>().pretty_name() << std::endl; // int&&
    return 0;
}
```

注意：const auto&& var 就不是万能引用了





万能引用的作用：

就是当参数为左值时，T&&为左值引用类型；当参数为右值时，T&& 为右值引用类型



引用折叠：引用折叠其实概念很简单&#x20;

一个引用不是左值引用就是右值引用，当一个万能引用被认为左值引用时，类型应该是 T& &&，此时类型就会折叠为T&。 <mark style="background-color:orange;">简单来说，就是引用符号太多了，折叠为“&”或“&&”</mark>
