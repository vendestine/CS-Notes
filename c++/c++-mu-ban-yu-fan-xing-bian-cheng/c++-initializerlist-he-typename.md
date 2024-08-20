# C++ initializer\_list和typename



initializer\_list 介绍：

initializer\_list 其实就是初始化列表，我们可以用初始化列表初 始化各种容器，比如“vector”，“数组”



首先我们把上次的MyArray类模板适配initialize\_list

<pre class="language-cpp"><code class="lang-cpp"><strong>// myArray.hpp
</strong><strong>#pragma once
</strong>#include &#x3C;type_traits>

template&#x3C;typename T>
struct get_type
{
    using type = T;
};


template&#x3C;typename T>
struct get_type&#x3C;T*>
{
    using type = T;
};

template&#x3C;typename T>
class MyArray
{
    using iterator = T*;
    using const_iterator = const T*;
public:
    MyArray(size_t count);
    MyArray(const std::initializer_list&#x3C;T>&#x26; list);
    MyArray(std::initializer_list&#x3C;T>&#x26;&#x26; list);

    ~MyArray();

    iterator begin() const;
    const_iterator cbegin() const;

    T&#x26; operator[](unsigned count) const
    {
        return data[count];
    }
private:
    T* data;
    size_t size_ = 0;
};

template&#x3C;typename T>
MyArray&#x3C;T>::MyArray(size_t count)
{
    if (count) {
        data = new T[count]();
    }
    else {
        data = nullptr;
    }
    size_ = count;
}


template&#x3C;typename T>
MyArray&#x3C;T>::~MyArray()
{
    if (data) {
        if (std::is_pointer&#x3C;T>::value) {
            for (size_t i = 0; i &#x3C; size_; i++) {
                delete data[i];
            }
        }
        delete[] data;
    }
}


// 注意一定要加入typename，否则会报错
// 类 + ::后 编译器会把后面的东西当作成员变量或者成员函数，加了typename后就当作类型了

template&#x3C;typename T>
typename MyArray&#x3C;T>::iterator MyArray&#x3C;T>::begin() const
{
    return data;
}

template&#x3C;typename T>
typename MyArray&#x3C;T>::const_iterator MyArray&#x3C;T>::cbegin() const
{
    return data;
}


template&#x3C;typename T>
MyArray&#x3C;T>::MyArray(const std::initializer_list&#x3C;T>&#x26; list)
{
    if(list.size()) {
        unsigned count = 0;
        data = new T[list.size()]();
        if (std::is_pointer&#x3C;T>::value) {
            for (auto elem : list) {
                data[count++] = new typename get_type&#x3C;T>::type(*elem);
            }
        }
        else {
            for (const auto&#x26; elem : list) {
                data[count++] = elem;
            }
        }
        size_ = list.size();
    }
    else {
        data = nullptr;
    }

}

template&#x3C;typename T>
MyArray&#x3C;T>::MyArray(std::initializer_list&#x3C;T>&#x26;&#x26; list)
{
    if (list.size()) {
        unsigned count = 0;
        data = new T[list.size()]();
        for (const auto&#x26; elem : list) {
            data[count++] = elem;
        }
        size_ = list.size();
    }
    else {
        data = nullptr;
    }
}


// main.cpp
#include &#x3C;iostream>
#include &#x3C;vector>
#include &#x3C;vld.h>
#include "myArray.hpp"


int main()
{
    int i1 = 10, i2 = 20, i3 = 30, i4 = 40;
    std::initializer_list&#x3C;int*> iList{ &#x26;i1, &#x26;i2, &#x26;i3, &#x26;i4 };
    MyArray&#x3C;int*> arrayPi(iList);
    for (unsigned i = 0; i &#x3C; 4; i++) {
        std::cout &#x3C;&#x3C; *arrayPi[i] &#x3C;&#x3C; std::endl;
    }
    return 0;
}
</code></pre>





typename的用法&#x20;

(1) 在定义模板时表示这个一个待定的类型&#x20;

(2) 在类外表明类的自定义类型时使用&#x20;

在C++的早期版本，为了减少关键字数量，用class来表示模板的参数，但是后来因为 第二个原因，不得不引入typename关键字。
