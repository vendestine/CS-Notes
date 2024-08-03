# QT信号槽



Demo：实现一个计数器，点击按钮一次，计数数值+1



首先设计一个界面，counter界面，所以类取名counter，生成counter.h和counter.cpp，counter.ui文件；一般一个界面对应三个文件；我们ui文件里设计界面的布局还有元素，C++文件里实现界面的逻辑和数据交互；



现在我们需要实现按钮点击，计数值 + 1；使用信号槽，我们有很多种实现方法；

1. QT Designer选中发出信号的对象，然后go to slot，实现槽（函数）

<div align="left">

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>slots关键字， 槽（函数）的声明</p></figcaption></figure>

</div>

<div align="left">

<figure><picture><source srcset="../.gitbook/assets/image.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/image.png" alt=""></picture><figcaption><p>实现槽（函数）</p></figcaption></figure>

</div>

2. 使用connet，连接信号和槽；QT4采用SIGNAL和SLOT宏，QT5直接使用函数指针

<div align="left">

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>QT4/QT5 connect不同写法</p></figcaption></figure>

</div>











Reference:

1. [https://doc.qt.io/qt-6/signalsandslots.html](https://doc.qt.io/qt-6/signalsandslots.html)
2. [https://www.cnblogs.com/schips/p/12537360.html](https://www.cnblogs.com/schips/p/12537360.html)
3. [https://www.cnblogs.com/jfzhu/p/13501678.html](https://www.cnblogs.com/jfzhu/p/13501678.html)
4. [https://blog.csdn.net/sazass/article/details/104903353](https://blog.csdn.net/sazass/article/details/104903353)
5. [https://blog.csdn.net/weixin\_40774605/article/details/109342536](https://blog.csdn.net/weixin\_40774605/article/details/109342536)
6. [https://juejin.cn/post/6847902223691677703](https://juejin.cn/post/6847902223691677703)
