# C++ 默认模板参数

默认模板参数：&#x20;

(1) 默认模板参数是一个经常使用的特性，比如在定义vector对象时，我们就可以使用 默认分配器。

&#x20;

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



(2) 模板参数就和普通函数的默认参数一样，一旦一个参数有了默认参数，它之后的参 数都必须有默认参数

(3) 函数模板使用默认模板参数

(4) 类模板使用模板参数



代码示例：

```cpp
#include <iostream>
#include <vld.h>
#include <vector>
#include <functional>
#include "myArray.hpp"


namespace mystd
{
    // 函数模板使用默认模板参数
    using void_int_func_type = std::function<void(int&)>;
    template<typename iter_type, typename func_type = void_int_func_type>
    void for_each(iter_type first, iter_type last, func_type func = [](int& elem) {
        ++elem;
        })
    {
        for (auto iter = first; iter != last; iter++) {
            func(*iter);
        }
    }

    // 类模板使用模板参数
    template<typename T, typename allocator_type = std::allocator<T>>
    class MyVector
    {
    public:
        template<typename T2>
        void outPut(const T2& elem);
    };

    template<typename T, typename allocator_type>
    template<typename T2>
    void MyVector<T, allocator_type>::outPut(const T2& elem) {
        std::cout << elem << std::endl;
    }
}
int main()
{
    
    std::vector<int> ivec{ 1, 2, 3, 4, 5 };
    mystd::for_each(ivec.begin(), ivec.end());
    for (int elem : ivec) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;


    mystd::MyVector<int> myVector1;
    return 0;
}
```
