# 简述多态实现原理

<figure><img src="../../.gitbook/assets/简述多态实现原理 (1).png" alt=""><figcaption></figcaption></figure>



多态算是比较高频的考点了，主要就是两点，静态多态和动态多态；

静态多态 就是 函数重载，函数重载的原理是函数名修饰

动态多态 就是 虚函数重写，运行时确定函数地址；



注意：

(1) 函数不能同时声明为virtual和static，所以只有成员函数可以修饰为虚函数

(2) 动态多态目的：希望 对象指针或者对象引用 调用成员函数的时候，可以调用指向对象的类中的成员函数，而不是指针或者引用的类中的成员函数；这个描述很重要，因为如果直接是使用对象调用成员函数的话，肯定是调用的对象的类中的成员函数，根本就不需要运行时确定函数地址！
