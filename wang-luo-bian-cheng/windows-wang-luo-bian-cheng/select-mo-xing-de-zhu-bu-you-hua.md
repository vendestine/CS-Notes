# select模型的逐步优化

我们之前已经写过了最基本的select模型



(1) C++封装：服务端封装一个Server类，客户端封装一个Client类

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>



（2) 粘包处理：

首先我们需要明白什么是粘包。 Send 函数的本质是将指定缓冲区的内容流到协议栈的发送缓冲区中。至于数据何时通过互 联网从本机发送给另外一台主机，这是操作系统要负责的事情。 操作系统的数据发送方式比较繁琐，但大致可以简单归结为以下两个条件满足一个：

1. 协议栈的接收缓冲区数据存在时间超过一定数值
2. 协议栈的接收缓冲区字符串长度超过一定数值；所以，当数据发送频率较快，或网络比较复杂，容易出现重发的情况时，就容易出现操作系 统一次发送了多个数据结构，半个数据结构的情况（比如一个login结构体就是一个数据结 构）。 对于数据接收端也是同理，recv函数的本质是将协议栈接收缓冲区中的内容流到指定的缓冲区中，至于是不是一个完整的数据结构根本不管。<mark style="background-color:orange;">于是便很容易出现一次接收了多个数据结 构或半个数据结构的情况。 对于这个问题，因为常常几个数据结构粘连在一起，于是我们给它起了个形象的名字：粘包</mark>。 上一节课为什么要设定发送间隔，发送的快了，就可能出现粘包问题。 至于粘包的处理方法，也很简单，就是创建一个第二缓冲区（又名消息缓冲区），第一缓冲 区就可以了，简单来说就是第一缓冲区接收数据，然后将数据复制到第二缓冲区中。所有的 处理部分都在第二缓冲区中进行。（这样就可以解决数据不全，多个数据粘连的问题）



(3) 多线程处理：

生产者消费者的核心就是一个线程生产数据，然后发送到一个公共缓冲区中，另外一个线程从该公共缓冲区中取出数据进行处理；服务端上，主线程接收客户端的链接，封装在Server中；子线程接收客户端发送的信息；子线程（公共缓冲区和消息处理）封装在recvServer中；



最终代码

服务端头文件

```cpp
// Client.h
// 服务端维护了很多个客户端socket，每个client socket维护了一个第二缓冲区
// client类维护 第二缓冲区和对应socket

#pragma once

#include<WinSock2.h>

const unsigned RECV_BUF_SIZE = 1024;

class Client
{
public:
	Client(SOCKET clntSock_);
	void setMsgBufCount(unsigned msgBufCount_);
	unsigned getMsgBufCount();
	char* getMsgBuf();
	SOCKET getSocket();
private:
	SOCKET clntSock;
	//第二缓冲区
	char szMsgBuf[RECV_BUF_SIZE * 10]{};
	//第二缓冲区的数据数量
	unsigned msgBufCount = 0;
};

// recvServer.h
// 子线程，接收客户端发送的信息
// 封装成一个接收服务器类 start开始子线程
// 需要封装客户端缓冲区，第一缓冲区
#pragma once
#include"Client.h"
#include"Message.hpp"
#include<map>
#include<vector>
#include<mutex>

class RecvServer
{
public:
	RecvServer();
	~RecvServer();
	void close();
	void start();
	void addClientToBufVec(Client* pClient);
	unsigned getClientNum();
private:
	void coreFunc();
	bool recvMsg(Client* pClient);
	bool conductMsg(Header* pHeader, Client* pClient);
	//客户缓冲区
	std::vector<Client*> clntBufVec;
	std::map<SOCKET, Client*> clntMap;
	//第一缓冲区
	char szBuf[RECV_BUF_SIZE]{};
	std::mutex _mutex;
};


// Server.h
// 主线程，接收客户端的链接
// 封装成Server类 start开始主线程
// 主线程中就包含一个存储子线程对应类的指针的容器。主线程可以通过指针对子线程发送消息
#pragma once
#include"recvServer.h"

const unsigned RECV_SERVER_SIZE = 4;

class Server
{
public:
	Server();
	~Server();
	void close();
	bool init(const char* ip, unsigned short port);
	bool start();
	void addClientToRecvServer(Client* pClient);
private:
	bool coreFunc();
	SOCKET servSock;
	std::vector<RecvServer*> recvServerVec;
};
```



客户端头文件

```cpp
// client.h
// 粘包处理 + 多线程优化
#pragma once
#include<WinSock2.h>
#include"message.hpp"

const unsigned RECV_BUF_SIZE = 1024;

class Client
{
public:
	Client();
	~Client();
	void close();
	bool init(const char* ip, unsigned short port);
	bool start();
	void recvMsg();   // 接收消息
	bool conductMsg(Header* pHeader);  // 处理消息
private:
	SOCKET servSock;
	//第一缓冲区
	char szBuf[RECV_BUF_SIZE]{};
	//第二缓冲区
	char szMsgBuf[RECV_BUF_SIZE * 10]{};
	//第二缓冲区数据长度
	unsigned msgBufLength = 0;
};
```



公共的消息类文件

```cpp
// message.hpp
#pragma once
#include<cstring>
enum class CMD
{
	LOGIN,
	LOGOUT,
	LOGIN_RESULT,
	LOGOUT_RESULT,
	NO_CMD
};

struct Header
{
	CMD cmd;
	unsigned dataLength;
	Header() :cmd(CMD::NO_CMD), dataLength(0) {}
	Header(CMD cmd_, unsigned dataLength_) : cmd(cmd_), dataLength(dataLength_) {}
};

struct Login : public Header
{
	char usrName[32];
	char passwd[32];
	Login(const char* usrName_, const char* passwd_) :Header(CMD::LOGIN, sizeof(Login))
	{
		strcpy_s(usrName, 32, usrName_);
		strcpy_s(passwd, 32, passwd_);
	}
	Login() :Header(CMD::LOGIN, sizeof(Login)), usrName{ 0 }, passwd{ 0 }{}
};

struct Logout : public Header
{
	unsigned logoutNum;
	Logout(unsigned logoutNum_) :Header(CMD::LOGOUT, sizeof(Logout)), logoutNum(logoutNum_) {}
	Logout() :Header(CMD::LOGOUT, sizeof(Logout)), logoutNum(0) {}
};

struct LoginResult : public Header
{
	unsigned loginResultNum;
	LoginResult(unsigned loginResultNum_) : Header(CMD::LOGIN_RESULT, sizeof(LoginResult)), loginResultNum(loginResultNum_) {}
	LoginResult() : Header(CMD::LOGIN_RESULT, sizeof(LoginResult)), loginResultNum(0) {}
};
struct LogoutResult : public Header
{
	unsigned logoutResultNum;
	LogoutResult(unsigned logoutResultNum_) :Header(CMD::LOGOUT_RESULT, sizeof(LogoutResult)), logoutResultNum(logoutResultNum_) {}
	LogoutResult() :Header(CMD::LOGOUT_RESULT, sizeof(LogoutResult)), logoutResultNum(0) {}
};
```
