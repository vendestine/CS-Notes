# lab 0

### 搭建实验环境

(1) 由于官方的sponge仓库已经不可见了，所以我们现在要自己建一个远程仓库，然后希望它和官方仓库一致。步骤如下：

1.1 clone远程仓库，然后本地创建同名分支和远程分支一一对应

单独的clone只会clone只会clone下来主分支，但是我们希望clone下来所有分支，所以就同时在本地创建同名分支和远程分支一一对应

<div align="left">

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

</div>

1.2 本地仓库的master分支回滚到lab0-startercode的commit；取消本地仓库和clone仓库的远程关联，添加自己的github远程仓库

<div align="left">

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

</div>

1.3 将本地仓库的所有分支push到自己的远程仓库中；现在我们自己的远程仓库就和官方的远程仓库一致了。

<div align="left">

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

</div>

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

1.4 远程仓库和官方的仓库一致了，每做一个lab就merge远程分支的lab-starter分支到本地分支上，然后最后本地的master分支push到远程仓库的master分支下。



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

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<div align="left">

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

</div>



(2) bytestream



2.1 记录一下遇到的问题

eof expectation老是出错，看一下这个test，其实就很明显了；读buffer时的eof需要两个条件，buffer为空，同时写入eof(input\_ended为ture)；

<div align="left">

<figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

</div>



write "cat"进capacity = 2的buffer出错

我最初的写法是 data.size() <= remain\_capacity()就写入，这个案例报错；

很明显，其实需求应该是截取一部分data去发送

<div align="left">

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

</div>



lab0 pass 100%

<figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>



2.2 思路和代码

lab0总体要求就是要得到一个字节流对象，所以封装设计了一个字节流类ByteStream

ByteStream类对象需要什么，很明显为了模拟字节流对象，我们需要一个队列来存储字节流，然后根据构造函数很明显，需要知道capacity；总体设计还是比较清晰。



实现成员函数的时候，我是先实现wirte（input)，然后实现read（output)；先分析一下成员函数之间是否有依赖关系；像read系列操作中的 read，peek\_out，pop\_out，很明显read是可以直接依赖peek\_out和pop\_out的；所以没必要从头写。总体来说，难度不大。

```cpp
class ByteStream {
  private:
    bool _error{};  //!< Flag indicating that the stream suffered an error.

    bool _end_input{};
    std::deque<char> _buffer{};
    size_t _capacity;
    size_t _bytes_read{};
    size_t _bytes_write{};

  public:
    //! Construct a stream with room for `capacity` bytes.
    ByteStream(const size_t capacity);

    //! \name "Input" interface for the writer
    //!@{

    //! Write a string of bytes into the stream. Write as many
    //! as will fit, and return how many were written.
    //! \returns the number of bytes accepted into the stream
    size_t write(const std::string &data);

    //! \returns the number of additional bytes that the stream has space for
    size_t remaining_capacity() const;

    //! Signal that the byte stream has reached its ending
    void end_input();

    //! Indicate that the stream suffered an error.
    void set_error() { _error = true; }
    //!@}

    //! \name "Output" interface for the reader
    //!@{

    //! Peek at next "len" bytes of the stream
    //! \returns a string
    std::string peek_output(const size_t len) const;

    //! Remove bytes from the buffer
    void pop_output(const size_t len);

    //! Read (i.e., copy and then pop) the next "len" bytes of the stream
    //! \returns a string
    std::string read(const size_t len);

    //! \returns `true` if the stream input has ended
    bool input_ended() const;

    //! \returns `true` if the stream has suffered an error
    bool error() const { return _error; }

    //! \returns the maximum amount that can currently be read from the stream
    size_t buffer_size() const;

    //! \returns `true` if the buffer is empty
    bool buffer_empty() const;

    //! \returns `true` if the output has reached the ending
    bool eof() const;
    //!@}

    //! \name General accounting
    //!@{

    //! Total number of bytes written
    size_t bytes_written() const;

    //! Total number of bytes popped
    size_t bytes_read() const;
    //!@}
};



#include "byte_stream.hh"
ByteStream::ByteStream(const size_t capacity) : _capacity(capacity) {}

size_t ByteStream::write(const string &data) {
    size_t write_count = min(data.size(), remaining_capacity());
    for (auto it = data.begin(); it < data.begin() + write_count; ++it) {
        _buffer.push_back(*it);
    }
    _bytes_write += write_count;
    return write_count;
}

//! \param[in] len bytes will be copied from the output side of the buffer
string ByteStream::peek_output(const size_t len) const {
    string str{};
    if (buffer_size() >= len) {
        for (auto it = _buffer.begin(); it < _buffer.begin() + len; it++) {
            str.push_back(*it);
        }
    }
    return str;
}

//! \param[in] len bytes will be removed from the output side of the buffer
void ByteStream::pop_output(const size_t len) {
    size_t read_count = 0;
    if (buffer_size() >= len) {
        int count = len;
        for (int i = 0; i < count; i++) {
            _buffer.pop_front();
            read_count++;
        }
    }
    _bytes_read += read_count;
}

//! Read (i.e., copy and then pop) the next "len" bytes of the stream
//! \param[in] len bytes will be popped and returned
//! \returns a string
std::string ByteStream::read(const size_t len) {
    auto str = peek_output(len);
    pop_output(len);
    return str;
}

void ByteStream::end_input() { _end_input = true; }

bool ByteStream::input_ended() const { return _end_input; }

size_t ByteStream::buffer_size() const { return _buffer.size(); }

bool ByteStream::buffer_empty() const { return _buffer.empty(); }

bool ByteStream::eof() const { return input_ended() && buffer_empty(); }

size_t ByteStream::bytes_written() const { return _bytes_write; }

size_t ByteStream::bytes_read() const { return _bytes_read; }

size_t ByteStream::remaining_capacity() const { return _capacity - _buffer.size(); }
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

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

