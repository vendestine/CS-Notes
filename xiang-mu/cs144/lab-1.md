# lab 1

## 实验流程





### Debug



(1)&#x20;



gdb调试了一下，发现问题出在eof上。测试中的代码打开以后看了一下，发现reassembler的eof，其实是byte\_stream读出eof！

byte\_stream读出eof依赖两个条件，一个是buffer\_size为空，满足了；一个是写入eof ，内部byte\_stream的\_end\_input为rue。

所以reassembler eof的时候，我们需要去手动设置  \_end\_input信号。

那么reassembler什么时候设置 \_end\_input信号呢，window为空，并且segment的eof出现过，自然就不再往byte\_stream里写入。

<figure><img src="../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

