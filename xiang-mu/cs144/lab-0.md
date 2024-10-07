# lab 0

### 搭建实验环境

(1) 由于官方的sponge仓库已经不可见了，所以我们现在要自己建一个远程仓库，然后希望它和官方仓库一致。步骤如下：

1. clone远程仓库，然后本地创建同名分支和远程分支一一对应

单独的clone只会clone只会clone下来主分支，但是我们希望clone下来所有分支，所以就同时在本地创建同名分支和远程分支一一对应

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

2. 本地仓库的master分支回滚到lab0-startercode的commit；取消本地仓库和clone仓库的远程关联，添加自己的github远程仓库

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

3. 将本地仓库的所有分支push到自己的远程仓库中；现在我们自己的远程仓库就和官方的远程仓库一致了。

<div align="left">

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

</div>

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

4. 远程仓库和官方的仓库一致了，每做一个lab就merge远程分支的lab-starter分支到本地分支上，然后最后本地的master分支push到远程仓库的master分支下。



### 实验流程

(0) pre

clone下来之后，按照要求，创建build目录

```
cmake ..
make format
make -j4
```

make format一直报错，这一块debug了一会，具体看debug1



(1) webget

这部分比较简单，我们需要实现get\_url这个函数，函数内部需要我们发送一个http请求，然后得到服务端返回的数据，打印出来。



我们需要使用CS144封装好的socket进行，网络通信。按照要求熟悉这两个类

<div align="left">

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

</div>

代码逻辑很容易实现，只要熟悉socket网络编程，就基本上能写出来。但是我遇到了一个问题，具体看debug2的记录，非常的坑。

```cpp
void get_URL(const string &host, const string &path) {
    // Your code here.

    // You will need to connect to the "http" service on
    // the computer whose name is in the "host" string,
    // then request the URL path given in the "path" string.

    // Then you'll need to print out everything the server sends back,
    // (not just one call to read() -- everything) until you reach
    // the "eof" (end of file).

    // 创建客户端socket，发出http连接请求
    TCPSocket sock;
    auto address = Address(host, "http");
    sock.connect(address);

    // 发送http请求报文
    sock.write("GET " + path + " HTTP/1.1\r\n");
    sock.write("Host: " + host + "\r\n");
    sock.write("Connection: close\r\n");
    sock.write("\r\n");

    // 循环读取服务端返回的信息并打印
    while (!sock.eof()) {
        auto str = sock.read();
        std::cout << str;
    }
    sock.close();

    cerr << "Function called: get_URL(" << host << ", " << path << ").\n";
    cerr << "Warning: get_URL() has not been implemented yet.\n";
}
```



### Debug&#x20;

(1) make format失败

参考：[https://blog.csdn.net/qq\_38161520/article/details/135886343](https://blog.csdn.net/qq\_38161520/article/details/135886343)

我是用的ubuntu环境，没有安装 clang-format，所以先安装clang-fortmat

```sh
sudo apt update
sudo apt install clang-format
clang-format --version    # 查看clang-format版本，验证是否安装成功
```

安装后仍然make format失败，cmake工程的配置文件有问题，打开sponge/etc/clang\_format.cmake修改配置文件

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>



(2) webget明明逻辑没问题，但是一直失败

http请求头 get这一行有问题

```cpp
// 必须写成这样
sock.write("GET " + path + " HTTP/1.1\r\n");

// 下面这样都是错的
sock.write("GET " + path + "HTTP/1.1\r\n");
sock.write("GET " + path + " HTTP/1.1 \r\n");
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

