# C++的标准输入输出

输入输出简单来说就是数据在输入设备，内存，硬盘，输出设备之间移动的过程。&#x20;

C语言设定了很多相关的函数实现这些过程；比如printf就是让数据从内存到显示屏（显示屏就是输出设备)； scanf 就是让数据从 键盘（键盘是输入设备）到内存，此外还有从内存到磁盘的文件操作函数。&#x20;

C语言的函数虽然简单方便，但彼此之间没有关联；<mark style="background-color:orange;">C++有了继承功能，可以让子类与父类之间有关联性，极大的提高各种输入输出功能之间的耦合性，于是 C++用继承功能重写了输入输出功能，这就是 io 库</mark>；

io 库引入了“流”的概念，数据从一个地方到另一个地方，原本地方的数据就没了，叫做流很贴切。&#x20;

io库是一个很大的部分，但现阶段我们只要会使用输入输出流，cout 和 cin 就可以了。 <mark style="background-color:orange;">cout可以让数据从内存流到输出设备，cin可以让数据从输入设备流到内存；</mark>