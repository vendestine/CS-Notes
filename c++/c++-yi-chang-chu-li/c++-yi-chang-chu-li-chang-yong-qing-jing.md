# C++ 异常处理常用情景

异常处理的前情提要：很多人不喜欢使用异常处理，认为它麻烦，应对可能出现的错误要 写那么多代码，会非常麻烦。 但实际上不是这样的，我们只需要在一些开发人员难以控制，比较容易出错的地方对异常 进行处理就可以了，需要进行异常处理的地方并不多。 举几个例子。



(1) 接收传递过来的被除数，我们难以判断被除数是否为0，此时异常处理就很有意义了。

```cpp
#include <iostream>

int divide(int divisor, int dividend) {
    if (!dividend) {
        throw "dividend is zero";
    }
    return divisor / dividend;
}


void clientInputNum(const std::string& str, int& num)
{
    std::cout << str << std::endl;
    while (std::cin >> num, !std::cin.eof()) {
        if (std::cin.bad()) {
            throw std::runtime_error("cin is corrupted");
        }
        if (std::cin.fail()) {
            std::cin.clear();   // 标志位清空 
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  // 缓冲区清空
            std::cout << "number format error, please input again" << std::endl;
            continue;  //再次进入循环,输入
        }
        break;   // 成功退出循环
    }
}

int main()
{
    int divisor = 0;
    int dividend = 0;

    clientInputNum("please input divisor", divisor);
    clientInputNum("please input divdend", dividend);

    try {
        std::cout << "ans: " << divide(divisor, dividend) << std::endl;
    }
    catch (const std::string& str) {
        std::cout << "exception occurs, exception is: " << str << std::endl;
    }
    catch (...) {   // 存在异常但是没有捕获到
        std::cout << "exception occurs, exception is: unknow exception" << std::endl;
    }

    return 0;
}
```



(2) 接收文件名，如果文件不存在，我们可以按照之前的写法要求重发一遍，也可以直接报异常，异常就是文件不存在

```cpp
#include <iostream>
#include <fstream>

void readFileContent(const std::string& fileName)
{
    std::ifstream ifs(fileName);
    std::string fileLineContent;

    if (ifs.is_open()) {
        while (ifs >> fileLineContent) {
            std::cout << fileLineContent << std::endl;
        }
        if (ifs.bad()) {
            throw std::runtime_error("ifs is corrupted");
        }
        ifs.close();
    }
    else {
        if (ifs.bad()) {
            throw std::runtime_error("ifs is corrupted");
        }
        if (ifs.fail()) {
            throw std::string("file not exist");
        }
    }
}

int main()
{
    // 这里输入偷懒了，其实应该用标准写法
    std::string str;
    std::cin >> str;

    try {
        readFileContent(str);
    }
    catch (const std::string& except) {
        std::cout << "exception occurs, exception is: " << except << std::endl;
    }
    catch (...) {
        std::cout << "exception occurs, exception is unknow exception" << std::endl;
    }
    return 0;
}
```



(3) 我们在动态分配内存时，经常出现内存不足的情况(在大型程序中，这是非常常见); 比如我们需要动态分配一个未知大小的数组，数组大小等待传入。使用new 操作符会直接抛 出bad\_alloc 的异常。

```cpp
#include <iostream>

int main()
{
    try {
        while (1) {
            int* pi = new int[1000000]();
        }
    }
    catch (const std::bad_alloc& except) {   // 捕获内置异常类对象 bad_alloc类对象
        std::cout << except.what() << std::endl;  // what是所有内置异常类都会有的函数
    }
    catch (...) {
        std::cout << "unknow exception" << std::endl;
    }
    return 0;
}
```



对new的处理非常重要，大家如果做专业的C++开发，会经常用到。 此外使用智能指针时如果内存分配不够也会抛出bad\_alloc的异常

```cpp
#include <iostream>
#include <memory>
#include <vector>

int main()
{
    std::vector<std::shared_ptr<int>> shared_int_vec;
    try {
        while (1) {
            std::shared_ptr<int> sharedI(new int[1000000]());   // 单独这么写是不会内存泄漏的，因为每次循环后sharedI销毁了，堆内存回收了
            shared_int_vec.push_back(sharedI);                  // push_back是push_back对象的拷贝，所以会调用shared_ptr的复制构造函数
            // 每次循环结束后sharedI销毁了，但是vector里的shared_ptr指向new出来的内存，所以该内存的引用计数为1，没有被销毁
            // 最后会造成内存泄漏 -> 即使使用只能指针还是有可能会造成内存泄露的问题
        }
    }
    catch (const std::bad_alloc& except) {   // 捕获内置异常类对象 bad_alloc类对象
        std::cout << except.what() << std::endl;  // what是所有内置异常类都会有的函数
    }
    catch (...) {
        std::cout << "unknow exception" << std::endl;
    }
    return 0;
}
```



(4) 有个vector，我们需要接受一个参数，然后取出参数对应的数组元素。此时就经常出现数组的越界问题

```cpp
#include <iostream>
#include <vector>

int main()
{
    std::vector<int> ivec{ 1, 2, 3, 4, 5 };
    try {
        ivec.at(10);   // 使用at才会抛出异常
        //ivec[10];      // []不会抛出异常
    }
    catch (const std::out_of_range& except) {
        std::cout << except.what() << std::endl;
    }
    catch (...) {
        std::cout << "unknow exception" << std::endl;
    }
    return 0;
}
```



更常见的用法

```cpp
#include <iostream>
#include <vector>

template<typename T>
class Test
{
public:
    T& operator[](unsigned count) const
    {
        if (count >= data.size()) {
            throw std::out_of_range("Test data is out of range");
        }
    }

private:
    std::vector<T> data;
};

int main()
{
    try {
        Test<int> t1;
        t1[10];   // 重载了[]运算符，现在越界会抛出异常
    }
    catch(const std::out_of_range& except) {
        std::cout << except.what() << std::endl;
    }
    return 0;
}
```
