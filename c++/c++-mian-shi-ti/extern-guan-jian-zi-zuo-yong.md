# extern关键字作用

<figure><img src="../../.gitbook/assets/extern 关键字作用.png" alt=""><figcaption></figcaption></figure>



我们使用一个变量或者函数的时候，编译器必须找到它的符号（所以我们得先声明它），extern的原理就是告诉编译器有这个符号，可以继续往下编译，引用还没有声明的变量或者函数，实际上这个变量或函数在其他地方声明了（当前文件也有可能是其他文件）；



