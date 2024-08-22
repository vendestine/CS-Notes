# C++ STL 仿函数

仿函数定义：就是一个可以调用“()”运算符的类对象，将operator()重载的类的对象就是仿函数。 简单来说，就是我们在用算法时最后一个参数需要一个可调用对象，STL本身已经帮我们定 义了很多可调用对象，不用我们自己再去定义了。



算术类仿函数&#x20;

加：plus\<T>

减：minus\<T>

乘：multiplies\<T>&#x20;

除：divides\<T>&#x20;

模取：modulus\<T>&#x20;

取负：negate\<T>



关系运算类仿函数&#x20;

等于：equal\_to\<T>

不等于：not\_equal\_to\<T>&#x20;

大于：greater\<T>&#x20;

大于等于：greater\_equal\<T>&#x20;

小于：less\<T>&#x20;

小于等于：less\_equal\<T>



逻辑运算仿函数&#x20;

逻辑与：logical\_and&#x20;

逻辑或：logical\_or&#x20;

逻辑否：logical\_no





仿函数的基本示例

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
    std::plus<int> iPlus;
    std::cout << iPlus(1, 2) << std::endl;

    std::vector<int> ivec{ 5, 2, 1, 6, 8, 2, 9 };
    //std::sort(ivec.begin(), ivec.end(), [](int elem1, int elem2) {
    //    return elem1 > elem2;
    //    });

    std::sort(ivec.begin(), ivec.end(), std::greater<int>());

    for (auto elem : ivec) {
        std::cout << elem << " ";
    }
    return 0;
}
```
