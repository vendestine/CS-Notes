# 关键字 override，final作用

<figure><img src="../../.gitbook/assets/虚函数的调用过程？.png" alt=""><figcaption></figcaption></figure>



总结：

override：重写虚函数容易写错，写错后就会生成新的虚函数，使用override，指定子类的一个虚函数重写基类的一个虚函数

final：阻止虚函数进一步重写，阻止类进一步派生
