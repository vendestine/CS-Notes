# C++ 智能指针

## 概念和用法



为什么要有智能指针

直接使用new和delete运算符极其 容易导致内存泄露，而且非常难以避免。于是人们发明了智能指针这种可以自动回收内存的工具



智能指针分类

> 普通的指针可以单独一个指针占用一块内存，也可以多个指针共享 一块内存。&#x20;

智能指针总共有三种：

(1) 共享型智能指针：shared\_ptr，同一块堆内存可以被多个shared\_ptr共享。&#x20;

(2) 独享型智能指针：unique\_ptr，同一块堆内存只能被一个unique\_ptr拥有。&#x20;

(3) 弱引用智能指针：weak\_ptr，也是一种共享型智能指针，可以视为对共享型智能指 针的一种补充