# malloc, free和new，delete的区别

<figure><img src="../../.gitbook/assets/malloc、free和new、delete的区别.png" alt=""><figcaption></figcaption></figure>



补充说明一下：

new是在free store上分配内存，因为new是重载了new操作符，我们可以在内部使用malloc（在堆上分配内存），也可以在其他分区分配内存（例如静态全局区的内存池）

