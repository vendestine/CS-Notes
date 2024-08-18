# QVector

QT容器 提供动态数组数据结构，跟std::vector类似，性能跟std::vector接近

QVector基于动态数组，在内存中，QVector元素是连续存储的，QVector适用于对访问速度要求较高的场景

QVector内存分配采用预分配策略，向QVector添加元素的时候，如果内存不足，QVector会重新分配一块更大的内存将原有的数据复制到新内存空间；为了减少内存分配复制操作的开销，QVector通常会预留一定的额外空间，这就意味着QVector实际容量大于当前存储数据的容量；

QVector删除元素时，通常不会立即释放内存，这样做是为了避免频繁的内存分配释放操作

QVector的优点：随机访问 连续存储 自动扩容

QVector的缺点：插入删除性能差，预分配策略可能导致资源浪费，不支持节点存储，在频繁插入删除的场景下不如QLinkedList效率高



QVector基本使用：

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    QVector<int> vec;
    vec.append(1);
    for (int i = 0; i < vec.size(); i ++) {
        qDebug() << vec[i];
    }
\
    qDebug() <<"====================";
    vec.push_back(2);
    vec.push_front(0);
    for (int i = 0; i < vec.size(); i ++) {
        qDebug() << vec.at(i);
    }

    qDebug() <<"====================";
    vec.insert(3, 3);   // insert是idx位置插入，然后idx和idx之后的元素往后移动
    vec.remove(0);
    for (int i = 0; i < vec.size(); i ++) {
        qDebug() << vec[i];
    }

    qDebug() <<"====================";
    qDebug() << vec.indexOf(2);   // 查找值为2的元素的index

}

```

