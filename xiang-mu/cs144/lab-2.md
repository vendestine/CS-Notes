# lab 2

### 实验思路

(1) wrap and unwrap

实现wrap和unwrap，我们的data index在tcp头中和在stream中的index是不一样的。因为tcp segment中，我们需要封装tcp header，tcp payload，空间有限。



转化思路

wrap比较简单，64位映射到32位，大值域 -> 小值域，这种情况映射唯一；

seqno = (abs\_seqno + isn) % 2^32 = abs\_seqno % 2^32 + isn ^ 2^32;

mod 2^32，可以自己取模，也可以转化为uint32\_t类型（利用了无符号类型的溢出环绕）



unwrap稍微困难一些，因为这是32位 映射到 64位，小值域 -> 大值域，这种情况不唯一，需要一个checkpoint来找到合法的seqno



一个巧妙的思路：先将64位的checkpoint映射到32位上，然后计算checkpoint和32位的seqno的diff，之后在64位的空间里，将checkpoint + diff，得到转化后的abs\_seqno；

其实看重载操作符，可以看出来，官方是希望我们这样求解；

由于diff可能是负数，所以得到的abs\_seqno也可能是负数，我们希望得到的abs\_seqno实际上是要早\[0, 2^32)间，所以如果是负数，+ 2^32就可以得到想要的abs\_seqno；

那有可能checkpoint + diff后溢出吗，其实没有可能，因为diff < 2^32，然后checkpoint为了选出\[0, 2^32)的abs\_seqno，不会比2^33大，因为如果比2^33大，此时就无法选出正确的abs\_seqno了，所以不会溢出。

<div align="left">

<figure><img src="../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

</div>



(2) receiver

receiver要实现的函数不多，主要就是segmnet\_received这个函数。

要正确优雅的实现，关键是看懂状态机，tcp receiver有三个状态

<div align="left">

<figure><img src="../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

</div>



listen：此时syn没有收到，ackno是空

syn\_recv：此时收到了syn，但是fin segment还没有被窗口滑出 = input\_ended()

fin\_recv：此时收到了fin，并且fin segment已经被窗口滑出



有了这三个状态后，就比较好写了。

其中要注意的点，一个segment有可能会在函数里进入这三个状态！所以不能写if elseif else这种结构



listen状态：检查segment是否有syn，有的话设置\_isn

syn\_recv状态：包含了所有发数据的情况，此时计算出push\_substring里需要的参数，index，data，eof；其中index的计算需要使用unwrap，checkpoint就是window\_start。

然后就是abs\_seqno -> stream\_index，如果是abs\_seqno是isn，那么直接是0，否则 - 1，如果abs\_seqno是fin，-1后转化过后的index非法，不会滑入窗口。



ackno()就比较简单了，如果没有isn，那么ackno()不存在

如果有，那么计算ackno，使用wrap函数，先要得到abs\_ackno

abs\_ackno = ack\_index + 1 + \_reassembler.stream\_out().input\_ended()

syn ack的情况是空；fin ack的情况，就是窗口已经滑出了fin\_segment，此时需要再 + 1；



### Debug

(1) 同时发syn + data + fin报错

<figure><img src="../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

我在segment\_received函数里按照状态机，对收到的segment进行处理，用的if elseif else结构，这样写的话，对于一个segment只会进入其中一种状态。

但是很明显，对于这种情况的segment，三个状态都会进入，所以修改结构，使三个状态都有可能进入。



(2) recv\_connect  ackno错误

2.1&#x20;

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

原因：listen状态下，直接return了，所以后面无法更新abs\_ackno

<div align="left">

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

</div>

解决：改成else return



2.2

<div align="left">

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

</div>

原因：fin没有统计进入abs\_ackno，虽然我的代码里明明有统计fin，但是判断是否fin的代码我放在了最后面，所以统计abs\_ackno的时候，fin\_sent还没来有更新。

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

解决：将判断fin的代码移到最前面



(3) recv\_transmit ackno错误

3.1

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

原因：push进reassmbler后，abs\_ackno其实会发生变化，但是我们在ackno()里，没有拿到最新的abs\_ackno，所以错误。

解决：ackno()里，更新abs\_ackno; 但是发现ackno()是一个常方法，没法更新abs\_ackno成员变量，所以改为在segment\_received里push后再更新一次。



改完这波后还剩下两个test了，大部分都通过了。

<div align="left">

<figure><img src="../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

</div>



(4) recv\_close错误

4.1&#x20;

<figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

带fin的segment发出后，abs\_ackno错误，没有调试，初步判断应该是fin相关的代码有些问题，定位到问题。计算data\_segment的index时，应该使用之前的abs\_ackno，但是我现有的代码，因为将fin的代码移动到最前面，所以导致在此之前计算的abs\_ackno加入了fin\_sent，但是fin\_sent计入abs\_ackno，应该是在push\_substring之后，所以解决就是直接使用之前的abs\_ackno就行了，push前不要计算abs\_ackno。



4.2&#x20;

<figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

调试了一下，应该是index计算错误了



4.3&#x20;

<figure><img src="../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

求ackno的时候，很明显abs\_ackno错误，窗口更新了，但是abs\_ackno没有更新；

我们虽然在push\_substring后更新，看似更新到了最新的abs\_ackno给akcno()用，但是push\_substring后，在到ackno()之间，其实有很多方法还是可以影响到abs\_ackno，所以这里必须先计算最新的abs\_ackno，考虑到ackno()，是const方法，我们abs\_ackno使用局部变量，不再声明为成员变量。



(5) special case出错

<div align="left">

<figure><img src="../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

</div>

之前的代码里，fin\_recv状态是通过fin\_sent判断的，但是后来发现fin\_recv不仅仅要fin\_sent，还要fin\_segment已经从窗口滑出了。



(6) 非法的seqno

<figure><img src="../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

一个比较特殊的case，这种情况下，非法，data不应发送；

我在计算index的时候，abs\_seqno是syn时，index = 0，其他的时候index = abs\_seqno - 1；

我的代码里是利用了 abs\_seqno = 0来判断是否是syn；

但是该case钻了一个漏洞，正常情况应该是abs\_seqno =0, 带syn，但是它构造了一种非法的状况 abs\_seqno = 0, 不带syn，此时这种情况得到index = 0，但实际上对应的应该是非法的index；

所以直接使用 header.syn()来判断，这样就会index = - 1，得到一个非法的index

<div align="left">

<figure><img src="../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

</div>

改完以后终于通过了。

<div align="left">

<figure><img src="../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

</div>
