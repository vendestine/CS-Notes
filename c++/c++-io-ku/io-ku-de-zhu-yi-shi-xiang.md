# io库的注意事项

io 对象无法使用拷贝构造函数和赋值运算符；所以我们使用流对象无法使用值传递，一般使用引用进行传递。

```cpp

std::istream myCin(std::cin);   // 无法使用拷贝构造函数

std::fstream fs1;
std::fstream fs2;
fs1 = fs2;                     // 无法使用赋值运算符
```



Io 对象的状态&#x20;

(1) io 操作是非常容易出现错误的操作，一些错误是可以修复的，另一部分则发生在 系统更深处，已经超出了应用程序可以修正的范围。&#x20;

比如我们使用cin向一个int类型的数中输入一个字符串，会使cin这个对象出现错误。

所以我们在使用io对象时都应该判断io对象的状态。 比如：while(cin >> val) 或 if(cin >> val)（不要只用这两个进行控制，最好搭配 iostate 来使用）&#x20;

(2) 我们需要知道流对象错误的原因，因为不同的错误需要不同的处理方法。&#x20;

io 库定义了iostate 类型，可以完整的表示io对象当前的状态。在不同的平台中， iostate 实现方法略有区别，在vs中直接用int来代表iostate类型，将不同的位置1 以表示不同的状态。可以与位操作符一起使用来一次检测或设置多个标志位。&#x20;

可以用rdstat函数来获得io对象当前用iostat类型来表示的状态

(3) iostata 类型有以下状态&#x20;

1. badbit状态，系统级错误，一旦表示badbit的位被置为1，流对象就再也无法 使用了。&#x20;
2. failbit 状态，代表可恢复错误，比如想读取一个数字却读取了一个字符，这种 错误就是可以恢复的。当badbit位被置1时，failbit位也会被置1。&#x20;
3. eofbit状态，当到达文件结束位置时，eofbit和failbit位都会被置1。&#x20;
4. goodbit状态，表示流对象没有任何错误。&#x20;

只要badbit，failbit，eofbit 有一位被置为1，则检测流状态的条件就会失败。&#x20;

(4) 标准库还定义了一组成员函数来查询这些标志位的状态 &#x20;

1. good()函数在所有错误位均未置1的情况下返回true。&#x20;
2. bad()，fail()，eof()函数在对应位被置1的情况下返回true。因为badbit位被置 1 或eofbit 位被置 1 时，failbit 位也会被置为 1。所以用 fail()函数可以准确判 断出流对象是否出现错误。
3. 实际上，我们将流对象当做条件使用的代码就等价于“!fail()”&#x20;



(5) 流对象的管理&#x20;

1. rdstate 函数，返回一个iostate值，对应当前流状态&#x20;
2. setstate(flag) 函数，将流对象设置为想要的状态&#x20;
3.  clear函数：是一个重载的函数。

    1\) clear()，将所有位置0，也就是goodbit状态。

    2\) clear(flag)，将对应的条件状态标志位复位。&#x20;
4. ignore函数： 作用：提取输入字符并丢弃他们。 函数原型：istream& ignore (streamsize n = 1, int delim = EOF) 读取到前n个字符或在读这n个字符进程中遇到delim字符就停止，把读取的这些东西丢掉



以上的理论知识都是为了这段标准输入处理代码服务的

```cpp
#include <iostream>
#include <fstream>

int main()
{
    int i = 10;
    while (std::cin >> i, !std::cin.eof()) {
        if (std::cin.bad()) {   // 出现系统级错误
            throw std::runtime_error("cin is corrupted");
        }

        if (std::cin.fail()) {  // 出现可恢复错误
            std::cin.clear();   // 清理标志位
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  // 清理缓冲区
            std::cout << "data format error, please try again" << std::endl;
            continue;
        }
        std::cout << i << std::endl;
    }
    std::cout << "process complete" << std::endl;
    return 0;
}
```
