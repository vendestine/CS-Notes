# 内存之中对于字符串的操作 sstream

string 流介绍：string 流可以向string 对象写入数据，也可以从string对象读取数据。与文 件操作类似，只不过数据交互变成了从内存到内存。&#x20;

string流默认包含一个string对象，当然，我们也可以指定。



string 流有哪些&#x20;

(1) istringstream 从string对象读取数据&#x20;

(2) ostringstream 向string对象写数据&#x20;

(3) stringstream 既可以从string对象读取数据，也可以向string对象写数据



string 流对象继承自 iostream 对象，除了继承得来的操作，string 流对象还有自己的成员 来管理流相关的string。&#x20;

(1) 对于string 流，io 库是没有像 cout，cin 这样的自定流对象的。流对象需要我们自己去定义&#x20;

1. stirngstream strm：stringstream代表一个string流对象的类型，strm是一个<mark style="background-color:orange;">未绑定</mark>的string流对象，内部包含了一个空的string对象
2. stringstream strm(s)：strm是一个<mark style="background-color:orange;">绑定了s的拷贝</mark>的string流对象。s是一个string对象&#x20;

(2) strm.str()：返回strm所保存的<mark style="background-color:orange;">string的拷贝</mark>。&#x20;

(3) strm.str(s)：将s的拷贝绑定到strm中，返回void



string 流对象的作用&#x20;

(1) 对数据类型进行转化，也就是string和其它类型的转化，这是string流对象最重要的功能。&#x20;

1. string 转化为int等类型。
2. int等类型转化为string。

(2) 用于对空格分隔的字符串的切分







相关代码

```cpp
#include <iostream>
#include <sstream>


int main()
{
#if 0   // string -> int
    std::string str("12");
    std::stringstream strStream(str);
    int i = 0;
    strStream >> i;

    // 异常处理
    if (strStream.bad()) {  // 系统错误
        throw std::runtime_error("strStream is corrupted");
    }
    else if (strStream.fail()) {  // 格式错误
        strStream.clear();
        strStream.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
        std::cout << "string format error" << std::endl;
    }
    else {
        std::cout << i << std::endl;
    }
#endif

#if 0   // int -> string

    int i = 100;
    std::stringstream strStream;
    strStream << i << std::endl;

    // 异常处理
    if (strStream.bad()) {
        throw std::runtime_error("strStream is corrupted");
    }
    else {
        std::cout << strStream.str() << std::endl;
    }
#endif



#if 1   // 切分
    std::string srcString("hello world nihao shijie");
    std::string destString;
    std::stringstream strStream(srcString);
    while (strStream >> destString) {
        std::cout << destString << std::endl;
    }

    // 异常处理
    if (strStream.bad()) {
        throw std::runtime_error("strStream is corrupted");
    }
#endif
    return 0;
}
```
