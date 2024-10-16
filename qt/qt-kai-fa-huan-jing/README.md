# QT开发环境

## QT下载安装

### 下载QT Maintenance Tool

由于Qt自`5.15`版本之后，就不再支持离线安装包了，所以想要继续安装，需要从官方下载在线安装工具

访问download.qt.io，然后选择archive，online\_installers，一般选择最新版本的installer，然后选择对应平台的installer安装文件；安装完成后，得到QT MainTenance Tool;

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### 安装QT以及相关组件

使用QT Maintenance Tool来管理QT以及相关组件，类似visual studio的installer；

打开Maintenance Tool后，登录，然后选择要安装的QT版本，以及需要的模块（一般要包含MinGW 32位编译器）；如果之后需要安装/卸载 其余的模块，也是使用QT Maintenance Tool管理；

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

使用QT的时候，一般选择MINGW32/MINGW64位编译器，一般不建议使用VC++编译器；

使用MINGW32编译的软件，是可以在32位操作系统，64位操作系统上运行的，一句话在所有windows上都可以跑；但是使用MINGW64开发出来的软件，只能在64位操作系统上跑；所以<mark style="background-color:orange;">推荐使用MINGW32</mark>；



如果使用官方源下载很慢，可以换源，使用国内镜像

```
# windows 使用installer或者安装好的MaintenanceTool都可以 
.\MaintenanceTool.exe --mirror --mirror https://mirrors.tuna.tsinghua.edu.cn/qt
.\installer.exe --mirror https://mirrors.tuna.tsinghua.edu.cn/qt

```

参考这篇文章：[https://www.cnblogs.com/ganshang/p/17213606.html](https://www.cnblogs.com/ganshang/p/17213606.html)





