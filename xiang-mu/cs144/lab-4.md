# lab 4



## 实验思路





状态机写法参考：

(1) [https://www.cnblogs.com/looking-for-zihuatanejo/p/17201366.html](https://www.cnblogs.com/looking-for-zihuatanejo/p/17201366.html)

(2) [https://www.epis2048.net/2022/cs144-lab4/](https://www.epis2048.net/2022/cs144-lab4/)





## 实验过程



### Debug

由于lab4的test过多，有的时候直接从terminal那看test信息很不方便；

我们也可以直接从/build/Testing下去看test的日志；主要就是看LastTest.log，LastTest.log.tmp, LastTestFailed.log这几个文件。

<div align="left">

<figure><img src="../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

</div>



(1) active\_close出错

<figure><img src="../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>



打开测试文件，断点查看，这个test出错了

<figure><img src="../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

之前的test，一般test代码的每一个excute只会封装一个action或者一个expectation；但是lab4的这些此时代码里，因为涉及到状态，所以一个excute里面会封装很多action和expectation，所以我们不必太去深究每一个excute里封装了些什么，只要看哪一步错了就行。

如何去看这个信息呢，action代表操作，expectation代表操作后应有的状态或者行为。

in\_closing封装了很多action和expectation，是为了让当前的peer达到closing状态。 &#x20;

<figure><img src="../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>



tick 10\*retx\_timeout - 1之前，peer的状态是没有问题的；tick之后sender和receiver的状态是没有问题的，但是active和linger的状态有问题。

debug之后，发现是因为tick后，\_time\_since\_last\_segment\_received 超过了10 \* retx\_timeout。

但实际上我们只tick了 10\*retx\_timeout - 1，理论上说没有超过，检查代码，发现segment\_received的时候，没有把\_time\_since\_last\_segment\_received重置为0，导致它加上了之前的tick的时间，所以超过；

解决：在segment\_received函数的头部，把\_time\_since\_last\_segment\_received重置为0。

<div align="left">

<figure><img src="../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

</div>



(2) winsize出错

<figure><img src="../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

调试了一下，发现当前的peer在收到ack空包后，没有继续fill\_window。检查了一下代码逻辑，我们是seg.length\_in\_sequence\_space() > 0时，才fill\_window，但是ack空包后不再这个范围里，所以我们没有继续fill\_window。

解决：收到ack后，要接着fill\_window。

<div align="left">

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

</div>



(3) ack\_rst出错

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

改完winsize后，ack\_rst突然就出错了，研究一下。

发现一个特殊的情况，当peer为listen状态时，收到ack空包，我们的代码会发送syn；但是官方要求，listen状态时，我们收到ack空包时，应该不发包。所以对这里我们特判一下；listen状态下，我们只能接收syn包，如果是ack包我们自动丢弃。



下面是官方对listen状态的具体描述：

<div align="left">

<figure><img src="../../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

</div>

<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>



加上corner case，listen状态时，不接受ack；修改之后，总算是通过了本地的所有test了。

<div align="left">

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

</div>



(4) txrx.h

test 104 - 151 failed or timeout

<div align="left">

<figure><img src="../../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

</div>

网上找了两份pass 100%的代码，做了一下测试，发现也是104-151有问题。

网上查了一下原因，应该是环境的原因，但是sponge的官方镜像已经关闭了，所以这里使用最新的2024 winter给出的镜像 + virtual box。



注意一定要用virtual box，vmware引入该镜像丢失了很多信息，例如没有端口转发配置，所以ssh无法连接，这里浪费了很多时间。



最后测试成功，pass 100%，证明我的实现是没有问题的。

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>



## 优化



经过吞吐量测试后，我们尝试进行优化



### 优化参考

(1) [https://www.misaka-9982.com/2023/02/19/CS144-Lab4/](https://www.misaka-9982.com/2023/02/19/CS144-Lab4/)

