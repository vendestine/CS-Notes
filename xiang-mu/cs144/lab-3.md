# lab 3

阅读官方lab manual: [https://github.com/LRL52/CS144-Sponge/blob/master/labs\_pdf\_21/lab3.pdf](https://github.com/LRL52/CS144-Sponge/blob/master/labs\_pdf\_21/lab3.pdf)



### Debug

(1) 定义定时器类的时候，发现size\_t报错unknow\_type

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

解决：引入头文件stddef.h即可



(2) TCPSender构造函数 初始化列表一直报错

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

很蠢的问题，我以为\_stream(capacity){}是在对 \_stream初始化，所以在新的一行尝试初始化timer，但是一直无法hint并且失败；

原因： \_stream(capacity){}中的{}是构造函数的函数体。。。

解决：在函数体前的初始化列表初始化

总结：该项目的C++代码风格其实挺不错的，我们可以借鉴，例如类中的成员变量，如果有默认值，直接在头文件中初始化，如果没有默认值，那么就在有参构造函数的初始化列表初始化；

有参构造函数的初始化列表初始化 使用make format后的格式，之后的代码里可以借鉴

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>
