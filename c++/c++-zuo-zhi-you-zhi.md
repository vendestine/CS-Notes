# C++ 左值 右值

### 左值，右值

<mark style="background-color:orange;">C++任何一个对象要么是左值，要么是右值</mark> int i = 10，i 和 10 都是对象，i是左值，10是右值；

&#x20;

左值：<mark style="background-color:orange;">拥有地址属性的对象就叫左值</mark>，左值来源于c语言的说法，能放在“=”左面的就是左值，注意，左值也可以放在“=”右面。&#x20;

右值：<mark style="background-color:orange;">没有地址属性的对象就叫做右值</mark>，注意，右值绝对不可以放在等号左面

> 有地址属性，就代表可以操作地址，没有地址属性，就无法操作操作地址；



一般来说， <mark style="background-color:orange;">判断一个对象是左值还是右值，就看对象有没有地址属性</mark>

比如<mark style="background-color:orange;">临时对象，就都是右值</mark>，临时对象没有地址属性，无法操作地址。 注意：左值也可以放在“=”右面，但右值绝对不可以放在等号左面



```cpp
#include <iostream>

int main() {
    int i = 10;
    int i2 = (i + 1);   // i + 1 临时对象 右值
    ++i = 200;          // ++i 先给i加1，然后返回i，i是有地址的，左值
    i++;                // i++ 先返回一个临时变量，临时变量的值 = i的值，然后临时变量的值 + 1
                        // 返回的是临时变量，当然无法使用地址
    return 0;
}
```

### 引用分类

普通左值引用：就是一个对象的别名，<mark style="background-color:orange;">只能绑定左值，无法绑定常量对象</mark>

```cpp
#include <iostream>

// 因为引用相当于别名，如果这里可以绑定的话，
// 我们只要修改refI的值，那么i的值可以绕过这个const修饰符而被修改，那么const就没有意义了

int main() {
    const int i = 100;
    int& refI = i;   // 非法，左值引用不允许绑定常量对象
    refI = 200;

    int j = 100;
    int& refJ = j;  // 合法
    
    return 0;
}
```



const 左值引用：可以对常量起别名，<mark style="background-color:orange;">可以绑定左值和右值</mark>

```cpp
#include <iostream>


int main() {
    const int i = 100;
    const int& refI = i;            // 绑定左值 合法
    const int& refI1 = (i + 1);     // 绑定右值 合法；

    return 0;
}
```



右值引用：只能绑定右值的引用

```cpp
#include <iostream>

// 右值引用 只能绑定右值
int main() {
    int i = 100;
    int&& rrefI = 200;   // 右值引用，绑定右值合法
    int&& refI1 = i;     // 右值引用，绑定左值不合法

    return 0;
}    
```



