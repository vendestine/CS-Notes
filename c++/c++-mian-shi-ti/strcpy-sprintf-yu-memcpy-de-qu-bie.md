# strcpy, sprintf与memcpy的区别

<figure><img src="../../.gitbook/assets/简述 strcpy、sprintf 与 memcpy 的区别？.png" alt=""><figcaption></figcaption></figure>



执行效率上，为什么memcpy > strcpy > spinrtf：

因为memcpy直接操作内存地址，strcpy需要判断/0，而sprintf中格式字符串涉及到类型转化。
