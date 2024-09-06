# C++的标准异常

(1) C++提供了一系列标准的异常，定义在头文件“”中，它们是以父子层次结构 组织起来的，如下图所示

<div align="left">

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

</div>

(2) 别看图很复杂，异常种类有很多，但经常使用的其实就几个。&#x20;

1. bad\_alloc 错误，使用new分配内存失败就会抛出bad\_alloc错误。&#x20;
2. out\_of\_range 错误，在使用 at 时，容器越界就会抛出这个错误，这也是“at”比 “\[]”更加优秀的原因
3. runtime\_error 错误，运行时错误，只有在程序运行时才能检测到的错误。这是一 个相对的概念，和logic\_error形成对比。logic\_error可以读代码读出来，runtime\_error 就不行。 我们也经常将一些读代码无法判断的异常标识为runtime\_error。&#x20;
4. ... 错误，可以接受任何错误，我们一般都会在catch最后加上“...”，这样就可以 接受所有类型的异常了。 剩下的异常使用频率比较低，但在某些情况下也是需要使用的。



(3) 自定义异常类型，其实需要自定义异常类型的情况真的非常少，这里就不介绍了，其实和标准异常也是一样的。



相关代码

```cpp
#include <iostream>
#include <vector>

int main()
{
    try {
        std::vector<int> ivec{ 1, 2, 3, 4, 5 };
        ivec.at(10);
    }
    // 其实直接使用基类exception也可以（多态的使用，父类引用可以接收子类对象），但是为了捕获异常的准确度一般更精细化以下
    //catch (const std::exception& except) {
    //    std::cout << except.what() << std::endl;
    //}
    catch (const std::out_of_range& except) {
        std::cout << except.what() << std::endl;
    }
    catch (const std::bad_alloc& except) {
        std::cout << except.what() << std::endl;
    }
    catch (...) {
        std::cout << "unknow exception" << std::endl;
    }
    return 0;
}



```
