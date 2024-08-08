# C++ 指针 引用



引用就是作用阉割（更安全）的指针（<mark style="background-color:orange;">可以视为“类型\*const</mark>”，所以<mark style="background-color:orange;">引 用必须上来就赋初值</mark>，不能设置为空），<mark style="background-color:orange;">编译器不将引用视作对象，操作引用相当于操作引用指向的对象</mark>。也就从根本是<mark style="background-color:orange;">杜绝了引用篡改内存的能力</mark>



进一步解释：

指针是一个变量，可以存储不同的地址（可以指向不同的内存）所以指针是可以修改内存的；引用是更安全的指针，它是类型\*const，存储的地址不能改变（和第一次指向的内存锁死了）它其实是一个指针常量，杜绝了引用篡改内存的能力；

```cpp
#include <iostream>


int main() {

    int i = 20;
    int& refI = i;
    int* const pi= &i;  // int& = int* cosnt  引用 = 指针常量

    std::cout << "refI: " << refI << ", *p: " << *pi << std::endl;

    return 0;
}
```

