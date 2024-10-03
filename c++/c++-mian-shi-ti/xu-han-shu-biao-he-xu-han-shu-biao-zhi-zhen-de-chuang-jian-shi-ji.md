# 虚函数表和虚函数表指针的创建时机

<figure><img src="../../.gitbook/assets/虚函数表和虚函数表指针（vptr）的创建时机.png" alt=""><figcaption></figcaption></figure>



注意：

(1) 继承的时候，如果子类没有重写虚函数，那么子类的虚函数表指针 还是指向 虚函数表地址
