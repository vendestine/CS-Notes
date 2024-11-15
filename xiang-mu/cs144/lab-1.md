# lab 1

## 实验流程



### 思路

lab1 主要是继续封装我们lab0实现过的bytestream对象，我们的byte\_stream对象，目前可以接收segment，但是接收到的segment有可能是乱序的，或者重叠的，所以我们在byte\_stream的上层继续添加重组的功能，封装成一个重组器。

<figure><img src="../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>



要实现reassembler，我们先理清一些概念。byte\_stream和reassembler的结构具体看这幅图

<div align="left">

<figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

</div>

byte\_stream：对应的绿色部分

read 数据从绿色部分进入蓝色部分；write 数据从红色部分进入绿色部分



reassembler：对应的绿色部分 + 红色部分

首先明白，reassembler要实现的就是红色的部分，红色的部分其实就是个滑动窗口。

push\_substring的功能：数据在窗口中的进行截断，放入窗口里；如果窗口里本来有字符，不需要放置，没有字符就放在窗口中的对应位置；

放入后检查窗口头部往后是否有连续字符，如果有那么就将窗口里的字符pop出去，写入byte\_stream；

最后要注意，如果byte\_stream进行了read操作，那么是会改变窗口的。所以push\_substring开始的时候需要先得到更新后的窗口。



<div align="left">

<figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

</div>





### Debug



先说一下调试遇到的困难，断点总是不准，很难定位到具体的语句，因为开了优化。

关闭优化！

<figure><img src="../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>



(1) singel测试文件，eof出错，我压根没有写eof相关的代码，我以为test会调用empty函数来判断eof。

gdb调试了一下，发现reassembler的eof，其实是byte\_stream读出eof，而不是简单的调用empty函数

byte\_stream读出eof依赖两个条件，一个是buffer\_size为空，满足了；一个是写入eof ：内部byte\_stream的\_end\_input为rue。

所以reassembler eof的时候，我们需要去手动设置  \_end\_input信号。

那么reassembler什么时候设置 \_end\_input信号呢，window为空，并且segment的eof出现过，自然就不再往byte\_stream里写入。

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>



(2) single测试文件，eof又出问题了，我之前是window为空 + segment出现过eof，设置bytestream写入eof信号；

但是该测试用例，segment出现过eof，window也为空，最后reassembler却不是eof；

原因：还有X字符没有接收

解决：在原来的条件基础上，再加上出现过eof的segment的最后一个字符，也已经被窗口接受过。

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>



(3) holes测试文件出错

<figure><img src="../../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

问题出在这，我们希望应该是 window\[0] = c, window\[1] = d，但是确实下面的结果

原因：之前模拟窗口滑动，我们讲window滑出去的部分置\0；如果滑出去后，窗口没有字符了，是没有问题。但是如果存在hole，窗口还有字符，那么该字符在新窗口中的位置就是错误的。

模拟：abd，ab滑出，d应该在新窗口的第二位，但如果只是窗口滑出去的部分置\0，那么d在新窗口的位置是第4位，错误。

解决：直接将窗口前面的字符pop出去，模拟滑动，这样d在新窗口中的第二位了。

<figure><img src="../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

修改成pop后，引发新的问题，原来是遍历窗口前面的元素，然后置\0；

改成pop后，就不是单纯的遍历了，而是循环pop出去。

<div align="left">

<figure><img src="../../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

</div>

改完这波以后，还剩3个test没过，继续debug...



(4) 空字符串的问题

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

现有的代码，我们无法判断该位置是放入了空字符，还是没有放入过字符，所以我们需要额外的操作区判断，可以使用一个同步的bitmap，表示窗口的某位置是否放入字符。

改完以后，通过了所有测试！

<div align="left">

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

</div>
