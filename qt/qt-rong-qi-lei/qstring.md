# QString

QString介绍：

标准C++提供了两种字符串 一种是以\0为结尾的字符数组即C风格字符串，另一种是std::sting. QT提供了自己实现的QString，功能更强大



QString是由一些列QChar组成的字符串；QString使用两个字节16位表示一个字符；使用的是UTF-16双字节编码；我们常见的编码比如UTF8，UTF8字符长度不固定导致字符串操作效率不高；QString采用固定长度的字符单元UTF16进行编码，字符串比较，查询效率更高一些

QString采用的是UTF16，C++标准采用的是UTF8编码，QT针对C++标准字符串提供了QBtyeArray类来操作UTF8编码的本地字符串，字节数组等



QString的优势：

(1) 使用隐式共享（写时复制）减少内存使用避免不必要的数据复制；减少CPU内存资源开销，提高效率

(2) 更多的操作 查询 转换函数 以及高效的内存分配优化

(3) 不用担心内存分配以及\0结尾，QString中间可以包含\0；length()函数返回整个字符串长度





QString基本使用：

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    // QSring初始化
    QString str = "hello world";

    // QString字符串拼接
    QString str1 = "!";
    str.append(str1);
    str.prepend("QT: ");

    qDebug() << str;   // 注意qDebug()输出完后自动换行
    qDebug() << str + str1 + str1;


    // QString格式化字符串
    int num = 10;
    double score = 10.2;

    QString str2 = QString("xiaoming's num is %1. score is %2").arg(num).arg(score);        // QString支持格式化字符串
    qDebug()<< str2;

    // std::string str3 = std::string("xiaoming num is %1, score is %2").arg(num).arg(score);  //std::string不支持格式化字符串
    // std::cout << str3 << std::endl;


    // QString转化为int
    QString str3 = "10";
    int num1 = str3.toInt();
    qDebug() << num1;

    // QString 转化std::string和C风格字符串
    qDebug() << "================================";
    QString str4 = str2;
    std::string string_str1 = str2.toStdString();
    const char* cstr1 = str2.toStdString().c_str();

    // std::string无法用qDebug()和printf输出，只能用std::cout输出
    // qDebug() << string_str1 << Qt::endl;
    // printf("%s", string_str1);
    std::cout << string_str1 << std::endl;

    // C风格字符串，pirntf, qDebug(), std:cout都可以输出
    qDebug() << cstr1;
    printf("%s\n", cstr1);
    std::cout << cstr1 << std::endl;

    // QString字符串，自然是只能用qDebug()输出
    qDebug() << str4;
    // std::cout << str4 << std::endl;
    // printf("%s\n", str4);
}

```

