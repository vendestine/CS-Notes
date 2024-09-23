# 如何避免野指针

<figure><img src="../../.gitbook/assets/如何避免野指针？.png" alt=""><figcaption></figcaption></figure>



野指针：指针指向一个随机地址

野指针如何产生的呢：

(1) 定义指针对象的时候，没有初始化；

(2) 指针指向的内存已经被释放，或者分配失败

