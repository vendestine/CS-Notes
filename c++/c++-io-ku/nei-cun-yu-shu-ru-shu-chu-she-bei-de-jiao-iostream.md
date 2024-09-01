# 内存与输入输出设备的交 iostream

(1) getline：getline比较常用，是std下的函数，getline其实并不复杂，不过是<mark style="background-color:orange;">按行接收数据，因为存储在string对象中，所以不容易出现格式错误</mark>， 但仍然可能出现系统错误，所以在企业级程序中，还是应当对bad的情况进行处理。



(2) get: get函数的用法和getline类似，是istream下的成员函数，只不过<mark style="background-color:orange;">get是以字符的格式进行接收</mark>。在企业级代码中仍然需要对bad的情况进行处理。



相关代码

```cpp
#include <iostream>
#include <string>


int main()
{
#if 0     // getline标准写法
    std::string str;
    while (std::getline(std::cin, str), !std::cin.eof()) {
        if (std::cin.bad()) {
            throw std::runtime_error("cin is corrupted");
        }
        std::cout << str << std::endl;
    }
#endif

#if 1    // get标准写法
    char c;
    while (std::cin.get(c), !std::cin.eof()) {
        if (std::cin.bad()) {
            throw std::runtime_error("cin is corrupted");
        }
        std::cout << c << std::endl;
    }
#endif
    return 0;
}
```
