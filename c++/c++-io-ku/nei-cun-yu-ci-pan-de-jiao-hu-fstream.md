# 内存与磁盘的交互 fstream

fstream 相对于 iostream。多了很多自己独有的操作&#x20;

(1) io 库默认没有给ifstream和ofstream类提供对象，需要我们自己去定义。&#x20;

(2) fstream 对象创建方式有三种&#x20;

1. 可以使用默认构造函数进行定义。例如： ifstream ifs
2. 也可以在创建流对象时打开想要打开的文件。例如ifstream ifs(s)。s可以是字符串，也可以是c风格的字符串指针。文件的mode依赖于流对象的类型。
3. 也可以在打开文件时就指定文件的mode。例如ifstream ifs(s, mode)&#x20;

(3) fstrm.open(s)函数，打开名为s的文件，并将文件与fsrm绑定，s可以是一个string， 也可以是一个c风格的字符串指针。&#x20;

(4) fstrm.close()函数，关闭文件。注意，一定不要忘了。&#x20;

(5) fstrm.is\_open()函数，返回一个 bool 值，指出与 fstrm 关联的文件是否成功打开且 尚未关闭。



fstream部分主要记住两段代码，

读文件：这段代码的目的是：让客户输入文件名称，如果文件不存在，就让客户重新输入文件名称，如果文件存在，就将文件全部内容输出。

写文件：将内容追加到文件中



文件模式：

(1) in 以读的方式打开&#x20;

(2) out 以写的方式打开&#x20;

(3) app 在进行写操作时定位到文件末尾&#x20;

(4) ate 打开文件后立即定位到文件末尾&#x20;

(5) trunc 截断文件（也就是文件已有的全部删除，重新开始写）&#x20;

(6) binary 以二进制方式打开文件



文件模式需要强调以下几点&#x20;

(1) 与ifstream 关联的文件默认in模式打开。&#x20;

(2) 与ofstream 关联的文件默认out模式打开&#x20;

(3) 与fstream 关联的文件默认in和out模式打开&#x20;

(4) 默认情况下，即使我们没有指定trunc，以out模式打开的文件也会被截断。为了 保持以out模式打开的文件的内容，我们必须同时指定app模式或in模式。&#x20;

(5) 只可以对ifstream或fstream的对象设定in的模式&#x20;

(6) 只可以对ofstream或fstream的对象设定out的模式&#x20;

(7) 只有当out模式被设置时才可以设置trunc模式&#x20;

(8) ate 和binary 模式可以应用与任何类型的文件流对象，且可以与任何其它文件模式 组合使用。&#x20;



总结：istream对象是 文件->内存，读文件操作时使用istream对象；ostream对象是 内存->文件，写文件操作时使用ostream对象；fstream对象 文件< - >内存，读/写操作可以使用fstream对象；





相关代码

```cilkcpp
#include <iostream>
#include <fstream>
#include <string>

int main()
{

#if 0   // fstream 对象创建方式有三种
    std::fstream fs("hello world", std::ios::in | std::ios::out);
    std::ifstream ifs("hello world");
    std::ofstream ofs;
#endif


#if 0  // fstream对象和文件绑定
    std::fstream fs;
    // 一个文件流对象 必须跟文件绑定才有意义
    fs.open("hello world");

    if (fs.is_open()) {  // 文件是否成功打开
        // 文件存储在内核区，需要自己销毁
        fs.close();
    }
#endif


#if 0   // 读指定文件的内容
    std::string fileName;
    std::string fileContent;

    while (std::cin >> fileName, !std::cin.eof()) {
        if (std::cin.bad()) {
            throw std::runtime_error("cin is corrupted");
        }

        // 读文件 使用ifstream
        std::ifstream ifs(fileName);
        if (ifs.is_open()) {
            while (std::getline(ifs, fileContent)) {
                std::cout << fileContent << std::endl;
            }
            if (ifs.bad()) {
                throw std::runtime_error("ifs is corrupted");
            }
            ifs.close();
        }
        else {
            ifs.clear();
            ifs.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            std::cout << "file not exist, please try again" << std::endl;
            continue;
        }
        std::cout << "process complete" << std::endl;
    }
#endif


#if 1    // 写内容到文件末尾
    std::ofstream ofs("test.txt", std::ios::app);
    if (ofs.is_open()) {
        ofs << "hello world" << std::endl;
        ofs.close();
    }
#endif
    return 0;
}
```

