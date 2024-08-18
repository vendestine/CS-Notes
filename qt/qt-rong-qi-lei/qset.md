# QSet

QSet是Qt提供的用于存储不重复元素的<mark style="background-color:orange;">无序</mark>集合。它是一个模板类，可以存储任意数据类型的元素。

QSet的底层实现是基于哈希表（hash table）的数据结构。哈希表是一种能够提供搞笑的插入，删除和查找操作的数据结构，它通过将元素映射到一个固定范围的同（buckets）中来实现这些操作

Qt中没有QMultiSet。在Qt中，你可以使用QList或QVector来实现类似于允许重复元素的集合的效果。这两个容器类都允许相同的元素出现多次，并按照插入的顺序来维护这些元素。

std::set是C++标准库中提供的一种有序集合容器，它基于红黑树实现。红黑树是一种自平衡的二叉搜索树，确保了树的深度在可接受范围内，从而保持了插入，删除和查找操作的高效性。



QSet基本使用：

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    qDebug() << "==============QSet=================";
    QSet<int> mySet;
    mySet.insert(1);
    mySet.insert(1);
    mySet.insert(2);
    mySet.insert(3);
    mySet.insert(4);

    QSet<int>::iterator it = mySet.find(4);
    mySet.erase(it);

    qDebug() << "size of the set: " << mySet.size();
    qDebug() << "Contents of the set: " <<mySet.values();

}

```
