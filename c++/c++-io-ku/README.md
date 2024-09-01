# C++ IO库

io 库介绍：

io 就是 input，output 的简写，也就是输入输出功能。<mark style="background-color:orange;">数据在内存，磁盘，输入输出设备之间移动就是io功能。</mark>

<div align="left">

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

</div>

io 库组成部分：&#x20;

(1) C++定义了ios 这个基类来定义输入输出的最基本操作，这个类的具体功能我们无需了解，只需了解C++io库所有的类都继承自这个类即可。&#x20;

(2) istream，ostream这两个类直接继承自ios类。&#x20;

1. ostream类定义了从<mark style="background-color:orange;">内存到输出设备（比如显示器）</mark>的功能，我们最常使用的cout就是ostream类的对象。&#x20;
2. istream类定义了从<mark style="background-color:orange;">输入设备（比如键盘）到内存</mark>的功能，我们最常用的cin就是istream类的对象。&#x20;
3. <mark style="background-color:orange;">iostream文件定义了ostream 和istream 类的对象，就是cout和cin</mark>。所以我们只要简单的引入iostream这个头文件，就可以方便的使用这两个对象&#x20;

(3) ifstream，ofstream类分别继承自istream类和ostream 类。&#x20;

1. ifstream 定义了从<mark style="background-color:orange;">磁盘到内存</mark>的功能。因为 istream 重载了“<<”运算符，所以ifstream 对象也可以用“<<”运算符来将文件数据写入内存。除了“=”的 所有重载运算符都是可以被继承的。&#x20;
2. ofstream定义了从<mark style="background-color:orange;">内存到磁盘</mark>的功能。与ifstream同理，也可以用“>>”操作 数据流。&#x20;
3. <mark style="background-color:orange;">fstream文件引入了ifstream和ofstream</mark>，所以我们只要引入ftream这个头文件，就可以使用文件流功能了。&#x20;



内存与输入输出设备的数据流动，磁盘与内存的数据流动已经介绍完了。<mark style="background-color:orange;">磁盘和输入输出设备直接无法直接交互，必须通过内存</mark>。&#x20;

> io 库还为我们额外定义了字符串的输入输出类，因为对字符串的操作极为频繁，所以这个库还是很有意义的。&#x20;

(4) istringstream，ostringstream 分别继承自 istream类和 ostream 类&#x20;

1. istringstream定义了<mark style="background-color:orange;">从指定字符串到特定内存</mark>的功能。与ifstream同理，也可以用“<<”运算符操作数据。&#x20;
2. ostringstream 定义了<mark style="background-color:orange;">从特定内存到指定字符串</mark>的功能。可以用“>>”操作数据。&#x20;
3. <mark style="background-color:orange;">sstream头文件就引入了istringstream 和 ostringstream</mark>，所以我们只要引入 sstream 这个头文件，就可以使用字符串与内存直接交互数据的功能。&#x20;



总结：

所以我们使用 io库主要就三个头文件，iostream（包含ostream类对象cout，istream类对象cin)，fstream（包含ifstream类，ofsream类），sstream（包含istringstream类，ostringstream类）

输入和输出都是相对于内存来说的，无论是输入输出设备，磁盘，字符串，我们讨论的输入输出都是相对于内存的；
