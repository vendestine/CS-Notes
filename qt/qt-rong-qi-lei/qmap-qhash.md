# QMap QHash

QMap和QHash都是QT框架中提供的关联容器，用于存储键值对。它们在内部分别使用红黑树和哈希表来实现。下面是它们的主要区别：



QMap

底层实现：QMap使用红黑树实现。这是一种自平衡的二叉搜索树，确保在插入和是你出操作后维持树的平衡。

有序性：QMap中的元素是有序的，按键的升序排列。这使得在遍历时元素是有序的。

性能特点：插入和查找操作的时间复杂度为O(logn)，其中n是QMap中元素的数量。



QHash

底层实现：QHash使用哈希表实现。哈希表使用哈希函数将键直接映射到表中的索引位置，以实现快速的插入，删除和查找。

无序性：QHash中的元素是无序的。在迭代时，元素的顺序可能不同于插入的顺序。

性能特点：插入和查找操作的平均时间复杂度为O(1)，但在极端情况下可能达到O(n)。



QMap和QHash共同点：

关联容器：QMap和QHash都是关联容器，用于存储键值对。

迭代器：它们都提供了迭代器，可以用来遍历容器中的元素。

接口相似：在接口上，它们提供了类似的成员函数来进行元素的插入，删除，查找等操作



使用建议：

如果你需要保持元素的有序性，并且希望在遍历时按键的升序排列，可以选择使用QMap。

如果对性能要求更高，不关心元素的有序性，可以选择使用QHash。



哈希冲突：

注意，QHash的性能依赖于哈希函数的质量，且可能存在哈希冲突的情况。在某些场景下，可能需要自定义哈希函数。总体而言，选择使用QMap还是QHash取决于你的具体需求。如果对元素有序性要求较高，或者需要在遍历时按键的升序排列，可以选择QMap。如果对性能要求更高，并且不关心元素的有序性，可以选择使用QHash。





QMap和QHash使用基本一致，区别就是在于底层实现；std::map和std::unordered\_map和QMap，Qhash也是基本一致的；



QMap基本使用：

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    QMap<QString, int> map;
    map.insert("Guangdong", 10);
    map.insert("Jiangsu", 9);
    map.insert("Hebei", 5);
    map.insert("Hainan", 1);
    qDebug() << "Map: " << map;

    qDebug() << "===============================";
    if (map.contains("Guangdong")) {
        qDebug() << "value: " << map.value("Guangdong");
    }

    QList<QString> keys = map.keys();
    QList<int> values = map.values();

    qDebug() << "keys: " << keys;
    qDebug() << "values: " << values;
    qDebug() << "size: " << map.size();

    qDebug() << "===============================";
    map.remove("Hebei");
    for (QMap<QString, int>::iterator it = map.begin(); it != map.end(); ++ it) {
        qDebug() << "key: " << it.key() << " value: " << it.value();
    }

    qDebug() << "===============================";
    for (const QString& key: map.keys()) {
        qDebug() << "key: " << key << " value: " << map.value(key);
    }

    map.clear();
    for (QMap<QString, int>::const_iterator it = map.constBegin(); it != map.constEnd(); ++ it) {
        qDebug() << "key: " << it.key() << " value: " << it.value();
    }

}

```



QHash基本使用

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    QHash<QString, int> hash;
    hash.insert("Guangdong", 10);
    hash.insert("Jiangsu", 9);
    hash.insert("Hebei", 5);
    hash.insert("Hainan", 1);
    qDebug() << "Hash: " << hash;

    qDebug() << "===============================";
    if (hash.contains("Guangdong")) {
        qDebug() << "value: " << hash.value("Guangdong");
    }

    QList<QString> keys = hash.keys();
    QList<int> values = hash.values();

    qDebug() << "keys: " << keys;
    qDebug() << "values: " << values;
    qDebug() << "size: " << hash.size();

    qDebug() << "===============================";
    hash.remove("Hebei");
    for (QHash<QString, int>::iterator it = hash.begin(); it != hash.end(); ++ it) {
        qDebug() << "key: " << it.key() << " value: " << it.value();
    }

    qDebug() << "===============================";
    for (const QString& key: hash.keys()) {
        qDebug() << "key: " << key << " value: " << hash.value(key);
    }

    hash.clear();
    for (QHash<QString, int>::const_iterator it = hash.constBegin(); it != hash.constEnd(); ++ it) {
        qDebug() << "key: " << it.key() << " value: " << it.value();
    }

    qDebug() << "===============================";
    if (hash.isEmpty()) {
        qDebug() << "Hash is Empty!";
    }

}

```
