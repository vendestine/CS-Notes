# C++ 类模板

## 概念和用法



类模板的写法与使用十分固定

```cpp
// myArray.hpp
#pragma once

template<typename T>
class MyArray
{
    using iterator = T*;
    using const_iterator = const T*;
public:
    MyArray(size_t count);
    ~MyArray();

    iterator begin() const;
    const_iterator cbegin() const;
private:
    T* data;
};

template<typename T>
MyArray<T>::MyArray(size_t count)
{
    if (count) {
        data = new T[count]();
    }
    else {
        data = nullptr;
    }
}


template<typename T>
MyArray<T>::~MyArray()
{
    if (data) {
        delete[] data;
    }
}


// 注意一定要加入typename，否则会报错
// 类 + ::后 编译器会把后面的东西当作成员变量或者成员函数，加了typename后就当作类型了

template<typename T>
typename MyArray<T>::iterator MyArray<T>::begin() const
{
    return data;
}

template<typename T>
typename MyArray<T>::const_iterator MyArray<T>::cbegin() const
{
    return data;
}


// main.cpp
#include <iostream>
#include "myArray.hpp"

int main()
{
    MyArray<int> array1(100);
    std::cout << *array1.begin() << std::endl;
    return 0;
}
```



注意事项

(1) 类模板中的函数，类外定义时一定要加模板头，然后返回类型前面得加上typename，说明此时是类型而不是命名空间。

