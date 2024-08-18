# QList

QList是QT框架中的一个动态数组容器，它提供了对元素高效操作，并支持动态增长和收缩；QList的实现原理基于动态数组，但也包含了一些优化，使其使用于不同的应用场景。

数组结构：QList内部使用数组（或类似数组的结构）来存储元素。元素在内存中是顺序存储的，可以通过索引直接访问元素。std::list是基于双向链表实现，内部元素存储分散。

动态增长：当添加元素导致数组空间不足时，QList会动态分配更大的内存空间，并将原有元素拷贝到新的内存空间中。这样，QList在进行添加操作时能够保持高效性能。

动态收缩：当从列表中移除元素导致数组空闲空间过多时，QList可能会缩小内存占用，释放多余的内存

快速插入和删除：在列表中间插入或删除元素时，QList可以通过移动元素实现，而不是简单地进行插入或者删除操作。这样做的好处是减少了元素移动的次数，提高了性能。



优势：随机访问速度快：由于元素是顺序存储的，QList提供了常数时间的随机访问速度。动态增长和收缩：QList可以自动调整内存大小，适应不同的数据凉，使其使用于动态变化的情况。

插入和删除效率较高：QList在插入和删除操作时采用了一些优化策略，可以提供相对高效的性能。

劣势：内存占用：由于QList时动态数组，可能会存在一些额外的内存占用，尤其是在数组空间动态增长和收缩时。非线程安全：QList不是线程安全的。在多线程环境中使用时，需要考虑同步机制。

使用建议：

对于小规模数据：当数据量较小时，QList提供了方便的操作和较小的性能开销。对于大规模数据：当数据量较大时，考虑使用QVector或QLinkedList，这些类在特定场景下可能提供更好的性能。

多线程环境：在多线程环境中，需要谨慎使用QList，可以考虑使用QReadWriteLock或其他同步机制确保线程安全。总体而言，QList是一个通用的，易于使用的容器类，适用于许多应用场景。在选择容器类时，建议根据具体的需求和性能特征选择合适的容器。



QList基本使用：

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    QList<QString> nameList;
    nameList << "Jimmy" << "Green" << "Curry" << "James" << "Page";

    for (const QString& name: nameList) {
        qDebug() << "Name" << name;
    }

    qDebug() << "===================================";
    nameList.prepend("Eric");
    nameList.append("Muffin");
    for (const QString& name: nameList) {
        qDebug() << "Name" << name;
    }

    qDebug() << "===================================";
    nameList.removeFirst();
    nameList.removeLast();
    nameList.sort();
    for (const QString& name: nameList) {
        qDebug() << "Name" << name;
    }

    qDebug() << "===================================";
    QList<int> nums;
    nums << 3 << 5 << 1 << 4 << 2;
    std::sort(nums.begin(), nums.end());
    for (const int& x: nums) {
        qDebug() << "num: " << x;
    }

}

```



