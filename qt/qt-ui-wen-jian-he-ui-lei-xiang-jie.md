# QT ui文件和ui类详解

## qt ui文件转化为一个C++的ui类

.ui文件介绍：

QT项目中 .ui文件是基于xml的界面文件；计算机行业很多框架都是基于xml，比如安卓

.ui文件最终是需要通过qt编译成C++代码的



QT xml文件什么时候转化为一个UI类的：

qmake后会构建项目的编译环境，生成debug release等文件夹（之后存放编译后的文件）还有Makefile（编译项目的文件）但是此时没有生成ui\_xxx类；我们是在build或者说make后，才生成了ui\_xxx类；这个时候，QT在成员初始化列表里面才能new ui，之后执行ui->setup();



QT ui类的位置：

ui类不在项目的工作目录里，而是在编译文件目录里，ui\_mainwindow.h文件是由.ui文件经过QT编译创建出来的；

<div align="left">

<figure><img src="../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

</div>

