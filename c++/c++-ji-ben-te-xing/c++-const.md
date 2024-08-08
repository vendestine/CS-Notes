# C++ const

const 关键字介绍：const是让<mark style="background-color:orange;">编译器将变量视为常量</mark>，用<mark style="background-color:orange;">const 修饰的变量和真正的常量有 本质的区别</mark>



<mark style="background-color:orange;">真正的常量存储在只读区中</mark> 【视编译器不同，我们这里的讨论的只读区中包含.text段(代码区） +  .rodata段(常量区）】&#x20;

比如“abcdefg”这个字符串就存储在常量区， 而“3”，“100”这些数字就存储在代码区中，这些都是真正的常量，无法用任何方式修改。

<mark style="background-color:orange;">const 修饰的变量仍然存储在堆区或栈区中，从内存分布的角度讲，和普通变量没有区别</mark>。

const 修饰的变量并非不可更改的，C++本身就提供了mutable 关键字用来修改 const 修饰的变量，从汇编的角度讲，const 修饰的变量也是可以修改的



代码分析：

```cpp
#include <iostream>
#include <string>

int main() {
    int i = 100;           // i在栈区
    const int i2 = 200;    // i2的值无法修改，但是i和i2在内存上是相邻的；i2也在栈区
    std::string str = "hello world";
    // 真正的常量 100 200 存储在代码区，"hello world"存储在常量区
    return 0;
}
```



[https://hjk.life/posts/segment-ram/](https://hjk.life/posts/segment-ram/)
