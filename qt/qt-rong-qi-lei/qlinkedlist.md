# QLinkedList

QLinkedList是QT框架中提供的双向链表容器，用于存储和操作元素。与QList不同，QLinkedList使用链表而不是数组来组织元素。

实现原理：双向链表结构：QLinkedList内部使用双向链表来存储元素。每个节点包含一个数据元素以及指向前一个和后一个节点的指针。

节点插入和删除：在链表中插入和删除节点的操作是高效的，不需要移动大量元素。这使得在链表中间插入或删除元素相对容易。

无需连续内存：由于链表的特性，QLinkedList不要求元素在内存中是连续存储的，这在动态变化的场景中提供了更大的灵活性。



优势：

插入和删除操作高效：在链表中插入和删除元素的操作不需要移动其他元素，因此在这些操作上 QLinkedList相对高效。

无需移动元素：当在链表中插入或删除元素时，不需要移动大量的元素，这些在某些场景下比数组更为高效。

无需连续内存：QLinkedList不要求元素在内存中时连续存储的，适用于动态变化的数据结构。



劣势：

随机访问相对较慢：由于链表的结构，QLinkedList提供的随机访问速度相对交满，需要遍历链表。

内存占用：与数组相比，链表可能存在一些额外的内存开销，每个节点需要存储指向前一个和后一个节点的指针。



使用建议：

插入和删除频繁：当需要频繁进行插入和删除操作时，QLinkedList提供了相对高效的性能。

动态变化的数据结构：当数据结构需要频繁动态变化，或者不确定元素数量时，链表可能是更好的选择。

随机访问较少：当随机访问的需求相对较少，而插入和删除操作更为频繁时，可以考虑使用QLinkedList。



QLinkedList QT框架已经不推荐使用，因为它跟std::list几乎一样

```
'QLinkedList<QString>' is deprecated: Use std::list instead
```



QLinkedList基本使用

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>
#include <QLinkedList>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    QLinkedList<QString> linkedList;
    linkedList << "Apple" << "Banana" << "Cherry";

    for (auto& item: linkedList) {
        qDebug() << "item: " << item;
    }

    qDebug() << "==========================";
    auto it = linkedList.begin();
    linkedList.insert(it, "Orange");
    it = linkedList.begin() + 1;
    linkedList.erase(it);
    for (auto& item: linkedList) {
        qDebug() << "item: " << item;
    }

    qDebug() << "==========================";
    linkedList.append("Grape");
    linkedList.prepend("Avocado");
    for (auto& item: linkedList) {
        qDebug() << "item: " << item;
    }

    qDebug() << "==========================";
    linkedList.removeFirst();
    linkedList.removeLast();
    for (auto& item: linkedList) {
        qDebug() << "item: " << item;
    }

}

```
