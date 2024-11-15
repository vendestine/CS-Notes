# CS模型

协议栈：

如果把整个互联网比作大海，那么一台主机就是一个孤岛，要把货物从一个孤岛送到另一个孤岛，需要层层包装；而在计算机中，一台主机要把一个信息发送给另一台主机，也需要各种协议将信息包裹，一个能实现这个功能的协议组合叫做协议栈；



CS模型中的函数：

<div align="left">

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

</div>

服务端就只需要记住4个函数：socket，bind，listen，accept

客户端只需要记住2个函数：socket，connect

剩下的send，recv，close都是一样的；



socket()：创建一个socket（网络协议栈的接口）

<pre class="language-cpp"><code class="lang-cpp">socket 函数 
SOCKET socket(
<strong>    int af, 
</strong>    int type, 
    int protocol 
)
af 参数代表协议族：主要有IPV4，IPV6。 
Type 代表socket 类型：常用的有SOCKET_STREAM，SOCKET_DGRAM。 
protocol 代表具体协议：常用的有IPPROTO_TCP,IPPROTO_UDP。 
这些不需要怎么理解，只要记住 
Socket (AF_INET, SOCK_STREAM, IPPROTO_TCP)代表 tcp 协议栈就可以了
函数成功返回协议栈句柄（文件描述符），失败返回-1（INVALID_SOCKET）
</code></pre>

connect()：向目标socket发出连接

```cpp
int connect(
    SOCKET s, 
    const sockaddr* name, 
    int namelen 
)
SOCKET:表示一个未链接的socket 
name: 表示套接字sockaddr结构体的指针 
namelen: sockaddr 的字节长度 
返回值：没有错误返回0，有错误，返回-1（SOCKET_ERROR) 
```

bind()：将指定socket绑定ip和port，在网络中定位

```cpp
int bind(
    SOCKET s, 
    const sockaddr* addr, 
    int namelen
); 
s 表示socket协议栈编号 
addr 表示sockaddr 结构体指针 
namelen 表示sockaddr 结构体长度 
返回值：没有错误返回0，有错误，返回-1（SOCKET_ERROR） 
Sockaddr 结构体没有类型，所以我们用与它内存分别完全相同的 sockaddr_in
来初始化sockaddr
```

listen(): 将指定socket设为接收状态

```cpp
int listen( 
    SOCKET s, 
    int backlog 
)
SOCKET:表示要将哪个协议栈设为接收状态 
Backlog:正在建立链接的客户端与等待建立链接的客户端之和 
返回值: 没有错误返回0，有错误，返回-1（SOCKET_ERROR）
```

accept()：监听指定socket上的连接

```cpp
SOCKET accept( 
    SOCKET s, 
    sockaddr* addr, 
    int* addrlen 
)
sockfd:套接字描述符，该套接口在listen()后监听连接。 
addr:(可选)指针，指向一缓冲区，其中接收为通讯层所知的连接实体的地址。
Addr 参数的实际格式由套接口创建时所产生的地址族确定。 
addrlen:(可选)指针，输入参数，配合addr一起使用，指向存有addr地址长
度的整型数。 
返回值：成功返回客户端协议栈，失败返回-1（SOCKET_ERROR） 
```

send(), recv()：发送/接收数据

<pre class="language-cpp"><code class="lang-cpp">int recv( 
    SOCKET s, 
    char* buf, 
    int len, 
    Int flags, 
<strong>) 
</strong>s:从哪个协议栈接收数据 
buf : 自己定义的接收缓冲区 
len: 自己定义的接收缓冲区大小 
flags: 接收方式，填0即可 
返回值： 
>0，接收数据的字符数 
=0，对面优雅断开了链接 
&#x3C;0，recv 函数出现错误 

int send( 
    SOCKET s, 
    char* buf, 
    Int len, 
    Int flags; 
)
s : 向哪个协议栈发生数据 
buf：发送缓冲区 
len：发送缓冲区中的数据长度 
flags：发送方式，填0即可 
返回值：若无错误：返回发送数据量，若有错误，返回-1（SOCKET_ERROR） 
注意，不会返回0.
</code></pre>

close()：关闭socket
