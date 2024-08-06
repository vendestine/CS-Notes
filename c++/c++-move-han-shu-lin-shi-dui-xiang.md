# C++ move函数 临时对象

move函数：

右值看重对象的值而不考虑地址，<mark style="background-color:orange;">move函数可以对一个左值使用，使操作系统不再在意其地址属性，将其完全视作一个右值</mark>

```cpp
#include <iostream>

// 右值引用 只能绑定右值
int main() {
    int i = 100;
    int&& rrefI = std::move(i);   // std::move(i)这个整体是右值
    i = 20;                       // 但是i还是左值，千万不要再使用i，否则move就没有意义了；
    std::cout << "i: " << i << " rrefI: " << rrefI << std::endl;
    return 0;
}
```

move函数让操作的对象失去了地址属性，所以我们有义务保证以后不再使用该变量的地址属性，简单来说就是<mark style="background-color:orange;">不再使用该变量</mark>，因为左值对象的地址是其使用时无法绕过的属性



临时对象：

右值都是不体现地址的对象。那么，还有什么能比临时对象更加没有地址属性呢？

<mark style="background-color:orange;">所以右值引用主要负责处理的就是临时对象</mark>。 程序执行时生成的中间对象就是临时对象，注意，所有的临时对象都是右值，因为临时对象产生后很快就可能被销毁，使用的是它的值属性
