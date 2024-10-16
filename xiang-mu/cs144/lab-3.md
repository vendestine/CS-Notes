# lab 3



### Debug

(1) 大部分test超时

原因：debug了一下，fill window的syn\_ack状态里，while死循环;

while循环的条件是 bytes\_in\_flight  < window\_size; 理论上说while循环内部会发segment，segment发出之后bytes\_in\_flight会增加，肯定会退出循环的。

但是有个特殊的情况，如果segment一直是空；再加上send的逻辑是 if (segment非空) send，此时一直不会send，bytes\_in\_flight不变，所以就会死循环。

解决：segment为空时，直接退出循环！



改完以后，超时问题解决，还剩一些test没通过了，继续debug...

<div align="left">

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>



(2) 重传syn失败

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

debug了一下，测试用例是tick了两次，两次加起来刚好一个rto的时间，check\_timeout成功，应该需要重传之前syn segment，但是我们没有重传syn，所以报错了。

我现在的tick代码是这样的，此时还没有ack，window\_size = 0，所以没有进入if语句内部，没有重传syn；

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

正常情况 window\_size = 0, 然后发出的第一segment，ack后；使sender端的window\_size更新为receiver端的window\_size(应该是receiver端的capacity)；之后再发送的segment，没有ack，超时后可以进行重传。

但有特殊情况：window\_size = 0时，然后此时发的第一个segment就ack失败，sender端的window\_size没有更新为receiver端的window\_size，此时tick超时后当前的segment不能重传，不符合我们的预期；



所以实验给出了解决方案，window\_size = 0的时候，window\_size更新成1；这样即使第一个发送的segment没有ack（没有更新成receiver的window\_size时），也可以超时后重传

<div align="left">

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

</div>

所以fill\_window里加上对window\_size的判断，如果 = 0，就更新成1

```cpp
_receiver_window_size = (_receiver_window_size == 0) ? 1 : _receiver_window_size;
```

改完以后，通过send\_retx！

<div align="left">

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

</div>

还剩两个test，继续debug

<div align="left">

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

</div>



(3) send\_close失败，诡异的错误

看到这个错误很懵，137和1差的也太多了；

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

先看一下close到底是执行了什么操作

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

看代码，就是sender的byte\_stream停止“生产”数据，然后fill\_window，把现有的数据都封装成segment都发出去。

debug以后发现了原因：明明发了fin segment，segment.length\_in\_sequence\_space() =1，再次进入循环后，又发了fin，所以一直循环，直到 byte\_in\_flight >= window\_size，才结束，所以最后bytes\_in\_flight = window\_size = 137;

解决：发过fin以后，就不能再发fin了，这样发过fin后的下一次循 segment.length\_in\_sequence\_space() =0，会退出循环。所以再判断是否发fin的时候，还要加上条件，!fin\_sent

```cpp
// 判断是否发fin
if (_stream.eof() && bytes_in_flight() + segment.length_in_sequence_space() < _receiver_window_size) {
    segment.header().fin = true;
    _fin_sent = true;
}

// 加上条件fin没有发送过才发送fin ！！！

// 判断是否发fin
if (!_fin_sent && _stream.eof() &&
    bytes_in_flight() + segment.length_in_sequence_space() < _receiver_window_size) {
    segment.header().fin = true;
    _fin_sent = true;
}
```



改完以后，send\_close pass，但是还剩一个test没有通过

<div align="left">

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

</div>



(4) send\_extra错误

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

看了一下测试用例，window\_size = 0的时候，超时需要重传，由于我们已经设置了window\_size = 0的时候，更新成1，所以按理说应该会重传的；事实证明上面的测试输出里，我们也重传了一次，但是没有继续重传。

window = 0时，无论重传多少次，每次的timeout时间就应该是初始rto时间；

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

所以我们需要在tick的代码，对于window = 0和非0的情况，做不一样的处理：

window\_size = 0，重传后rto不变

window\_size != 0, 重传后rto翻倍

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

修改后还是出错，debug后发现，tick的时候，window\_size不可能等于0！

原因：我们直接在fill\_window里更新了window\_size，所以选择不更新window\_size；



现在没有更新window\_size了，但是还是出错，为什么呢，看下面两个测试代码；

其实我们的需求是，window\_size = 0时，对于syn的重发遵循rto \* 2机制

但是window\_size = 0时，对于非syn的重发，我们是直接使用初始rto；

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

如果是发syn，window\_size = 0，我们直接把window\_size = 1，这样可以保证重发syn遵顼rto \* 2机制；

如果是发data，我们不修改window\_size



又出错了，如果ack后，window\_size = 0，我们就没法发送新的segment，因为此时bytes\_in\_flight = window\_size，没法发送segment了；所以我们需要将窗口 window\_size = 0的时候置为1，同时又保留原始window\_size的可能性；

所以我们可以使用一个局部变量window\_size，记录应该看作的window\_size；然后在发送segment的时候使用这个局部变量window\_size，而tick里超时重传使用原本的成员变量window\_size去判断；

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

修改后的代码：

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>



总算是pass了，这个lab总的来说内容还是比较多的，其中这个window\_size和超时重传的特殊情况还是比较难想的，只能通过test去理解。

<div align="left">

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

</div>





C++语法错误：

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



(3) Moving a temporary object prevents copy elision (fix available)

std::move()的参数应该是左值，如果参数是右值，就会报这个错误；

返回类型 是非引用或者是右值引用，返回值都是右值
