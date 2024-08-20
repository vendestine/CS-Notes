# C++ 函数模板



函数模板介绍：

普通函数模板，成员函数模板



普通函数模板，成员函数模板使用示例：

```cpp
#include <iostream>
#include <vld.h>
#include <vector>
#include "myArray.hpp"


namespace mystd
{
    // 普通函数模板
    template<typename iter_type, typename func_type>
    void for_each(iter_type first, iter_type last, func_type func) {
        for (auto iter = first; iter != last; iter++) {
            func(*iter);
        }
    }

    // 类模板
    template<typename T>
    class MyVector
    {
    public:
        // 成员函数模板
        template<typename T2>
        void outPut(const T2& elem);
    };

    // 成员函数模板类外定义
    template<typename T>
    template<typename T2>
    void MyVector<T>::outPut(const T2& elem) {
        std::cout << elem << std::endl;
    }
}
int main()
{
    // 测试普通函数模板
    std::vector<int> ivec{ 1, 2, 3, 4, 5 };
    mystd::for_each(ivec.begin(), ivec.end(), [](int& elem) {
        ++elem;
    });
    for (int elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;

    // 测试成员函数模板
    mystd::MyVector<int> myVec;
    myVec.outPut<int>(20);
    return 0;
}
```
