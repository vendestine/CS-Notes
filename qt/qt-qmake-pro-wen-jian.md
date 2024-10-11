# QT qmake pro文件

## QT项目的目录结构

根据一个最基本的QT application项目，看看QT项目的目录结构

<figure><img src="../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

QT项目里一般有这几部分文件

.cpp 源文件，.h 头文件

.ui 界面文件：界面脚本（xml)，点开后会跳转到design界面

.pro文件：项目组织文件

.pro.user文件：项目用户配置文件，记录了用户的一些个人配置

build文件夹：编译后的文件都放在这里



## pro文件详解

QT creator自动生成的 .pro文件 和 在命令行使用qmake -project命令生成的.pro文件，初始内容有一些区别；



QT Creator自动生成的 .pro文件：

<figure><img src="../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

qmake -project命令生成的 .pro文件

<figure><img src="../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

根据这两个文件的内容，解释一下.pro文件的信息

```properties
QT += core gui widgets 表示各种QT的模块
除了使用QT += 如果你使用QT去构建项目时某些模块默认加入了，也可以使用 QT -= 去掉这些模块
  
CONFIG += console C++17 debug warn_off exceptions no_exceptions stl no_st 
这些都是给项目添加一些配置，用的最多的是debug;
如果不加debug默认是release模式编译，加了debug后是debug模式编译

TEMPLATE = app lib subdirs 用于指定项目的类型
TARGET = pro_make 表示可执行程序的名字

win32 {   // 不同平台下 链接头文件和库文件的语法会有些区别
  INCLUDEPATH += . 包含的头文件的路径 
  eg: INCLUDEPATH += $$PWD/libmath/include    $$PWD表示当前文件夹目录
  LIBS += . 包含的库文件路径 
  eg: LIBS += -L$$PWD/libmath/lib -lccmath  -L后是库文件目录 -l表示库文件
}
  
SOURCES += main.cpp     项目需要的源文件
HEADERS += mainwindow.h 项目需要的头文件
FORMS += mainwindow.ui  项目需要的ui文件
```



.pro文件注意事项

(1) QT，CONFIG等关键字必须大写，否则无效

(2) 每次修改了pro文件后最好都去做一次qmake，否则新的配置不会不生效





## 生成.pro文件和项目编译

如何生成.pro文件：

(1) QT creator创建项目的时候，会自动生成一个.pro文件

(2) 在项目目录下，使用qmake -project命令，生成.pro文件

qmake -project: qmake -proejct命令把某个文件夹下的所有文件添加到pro下作为整个工程的一部分，主要是.h .coo .qrc .ui文件



如何编译文件：

(1) QT Creator打开项目，然后build（其实就是qmake + mingw32-make)，就可以编译文件，编译后的文件会存放在build文件下，不同平台和不同模式下的编译文件都会存放在build文件夹下

(2) 在项目目录下，创建build文件夹，然后在build文件夹里，qmake ..（qmake作用的目录是上级目录）生成Makefile文件，然后make编译项目

qmake：qmake会根据当前的.pro文件生成Makefle文件；Makefile文件描述了如何编译链接整个程序的文件

mingw32-make：make找到Makefile文件去编译程序；在windows上，由于使用的linux gcc g++移植到windows的mingw编译器，只能去使用mingw32-make；当然也可以自己去copy一下mingw32-make这个文件然后重命名make，这样就可以像linux平台一样直接使用make



qmake和mingw32-make在QT中的位置：

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>



命令行编译QT项目，视频演示：

[https://www.bilibili.com/video/BV1n1eVeJEAS/?spm\_id\_from=333.999.0.0\&vd\_source=cb02f779bd17a3aad9801e0c4464dfc9](https://www.bilibili.com/video/BV1n1eVeJEAS/?spm\_id\_from=333.999.0.0\&vd\_source=cb02f779bd17a3aad9801e0c4464dfc9)





&#x20;&#x20;
