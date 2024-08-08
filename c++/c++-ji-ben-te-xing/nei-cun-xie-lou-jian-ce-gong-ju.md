# 内存泄露检测工具



内存泄露是经常出现的常见 bug

内存泄露会导致堆内存的逐渐被占用，最终内存用完程序崩溃。常见的情况就是项目测 试没问题，上线几天就炸了。然后就会非常麻烦，排查困难，损失很大。 内存泄露是最严重的错误之一，程序不怕报错，就怕一开始运行的好好的，突然就出 现了莫名其妙的错误。



VLD，一款比较好的内存泄露检测工具



使用步骤:

1. 首先下载VLD
2. VS中<mark style="background-color:orange;">添加VLD的inlcude目录路径 到VC++ include目录</mark>；现在可以引入vld的头文件，不会报错了；但是编译还是会报错，因为头文件中使用的静态库在编译时没有链接进去
3. VS中<mark style="background-color:orange;">添加VLD lib目录对应平台的库路径 到VC++ 库目录</mark>；现在可以编译成功了，但是运行还是会报错，因为头文件中使用的动态库在运行时没有链接进去
4. <mark style="background-color:orange;">复制vld的bin目录下的所有文件（动态库文件）到VS项目的目录下</mark>；现在可以运行了



步骤演示：

<figure><img src="../../.gitbook/assets/媒体1898.gif" alt=""><figcaption></figcaption></figure>









