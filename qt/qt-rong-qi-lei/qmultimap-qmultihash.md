# QMultiMap QMultiHash

QMap和QMultiMap都是QT框架中的关联容器，用于存储键值对。它们的主要区别在于处理键冲突的方式和存储多个相同键的能力。

QMap对于相同的键，会覆盖旧值，保持每个键在容器中唯一。QMultiMap允许相同的键关联多个值。如果每一个键只与一个值相关联，可以使用QMap。如果需要一个键与多个值关联，可以使用QMultiMap。

QHash和QMultiHash都是Qt框架中的哈希表实现的关联容器，用于存储键值对。它们的主要区别在于处理键值冲突的方式和存储多个相同键的能力。

QHash对于相同的键，会覆盖旧值，保持每个键在容器中唯一。QMultiHash允许相同的键关联多个值。插入相同键的多个值会形成一个键的值列表。如果每个键只与一个值相关联，可以使用QHash。如果需要一个键与多个值关联，可以使用QMultiHash。



QMultiMap和QMultiHash使用：

```cpp
#include <QCoreApplication>
#include <QDebug>
#include <iostream>
#include <cstdio>

int main(int argc, char *argv[])
{
    Q_UNUSED(argc);
    Q_UNUSED(argv);

    qDebug() << "==============QMap=================";
    QMap<QString, int> myMap;
    myMap.insert("one", 1);
    myMap.insert("one", 111);
    myMap.insert("two", 2);
    myMap.insert("three", 3);

    QMap<QString,int >::const_iterator it;
    for (it = myMap.constBegin(); it != myMap.constEnd(); ++it) {
        qDebug() << "key: " << it.key() << " value: " << it.value();
    }

    qDebug() << "==============QMultiMap=================";
    QMultiMap<QString, int> myMultiMap;
    myMultiMap.insert("one", 1);
    myMultiMap.insert("one", 11);
    myMultiMap.insert("two", 2);
    myMultiMap.insert("three", 3);

    QMap<QString,int >::const_iterator it2;
    for (it2 = myMultiMap.constBegin(); it2 != myMultiMap.constEnd(); ++ it2) {
        qDebug() << "key: " << it2.key() << " value: " << it2.value();
    }


    qDebug() << "==============QHash=================";
    QHash<QString, int> myHash;
    myHash.insert("one", 1);
    myHash.insert("one", 11);
    myHash.insert("two", 2);
    myHash.insert("three", 3);

    QHash<QString,int >::const_iterator it3;
    for (it3 = myHash.constBegin(); it3 != myHash.constEnd(); ++ it3) {
        qDebug() << "key: " << it3.key() << " value: " << it3.value();
    }


    qDebug() << "==============QMultiHash=================";
    QMultiHash<QString, int> myMultiHash;
    myMultiHash.insert("one", 1);
    myMultiHash.insert("one", 11);
    myMultiHash.insert("two", 2);
    myMultiHash.insert("three", 3);

    QHash<QString,int >::const_iterator it4;
    for (it4 = myMultiHash.constBegin(); it4 != myMultiHash.constEnd(); ++ it4) {
        qDebug() << "key: " << it4.key() << " value: " << it4.value();
    }

}

```
