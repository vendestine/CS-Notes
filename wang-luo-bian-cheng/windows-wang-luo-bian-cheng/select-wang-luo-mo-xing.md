# select网络模型

之前的最基本的CS网络模型，只能支持一个客户端连接，因为accept()，recv()都是死等函数，没有接收到连接，或者没有接收到数据的时候，就会一直阻塞；这个时候提出了select模型，它可以支持多个客户端的连接；



```
int select(
    int nfds,                //windows 中无意义
    fd_set* fdRead,          //检查可读性
    fd_set* fdWrite,         //检查可写性
    fd_set* fdExcept,        //检查异常
    const timeval* timeout   //设定超时时间
)

struct fd_set(
    unsigend fd_count,          // 有多少socket
    SOCKET fd_array[FD_SETSIZE] // socket数组
)


返回值：     
> 0: 表示有客户端链接或有客户端发送消息 
= 0: 表示超时无客户端链接，也没有客户端发送消息 
< 0: 表示select函数出现错误 

```

