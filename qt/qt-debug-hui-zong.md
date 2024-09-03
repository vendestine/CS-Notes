# QT Debug汇总

### **QMetaObject::connectSlotsByName: No matching signal for on\_clickedButton\_clicked()**

原因：有`on_<objectName>_<signalName>`形式的函数并且找不到对象名为objectName的对象

解决办法：更改函数名，避免用`on_<objectName>_<signalName>`的槽函数，显示用connect连接信号和槽\


Reference：

[QMetaObject::connectSlotsByName: No matching signal for XXX 原理探究 - 康宇PL - 博客园](https://www.cnblogs.com/kangyupl/p/13525261.html)

[QMetaObject::connectSlotsByName: No matching signal for问题的解决方法 - etcjd - 博客园](https://www.cnblogs.com/chen-diary/p/6927885.html)



### Qt直接运行生成的exe文件提示“缺少Qt5Core.dll”的解决办法 <a href="#articlecontentid" id="articlecontentid"></a>

原因：没有将程序目录添加到环境变量

解决：添加到环境变量即可&#x20;



Reference:

[https://blog.csdn.net/liubing8609/article/details/87474900](https://blog.csdn.net/liubing8609/article/details/87474900)



### Qt窗口在多显示屏上不显示 <a href="#articlecontentid" id="articlecontentid"></a>

原因：QT5中，多显示屏情况下，destop()函数已经无法获取到对应屏幕的分辨率

解决：采用QScreen类或者使用desktop的index来对不同显示屏做不同的处理



Referene：

[https://blog.csdn.net/yanmiao\_liu/article/details/116262420](https://blog.csdn.net/yanmiao\_liu/article/details/116262420)         &#x20;

[https://blog.csdn.net/WL0616/article/details/129182751](https://blog.csdn.net/WL0616/article/details/129182751)

[https://www.cnblogs.com/GEEK-ZHAO/p/12370130.html](https://www.cnblogs.com/GEEK-ZHAO/p/12370130.html)





### QT自定义类添加Q\_OBJECT后 undefined reference to \`vtable for Window Widget'

原因：即使添加了Q\_OBJECT，还是没有生成相关的moc文件，需要rebuild一下

解决：rebuild project 或者 执行qmake



Reference:

[https://blog.csdn.net/qq\_25430563/article/details/121704641](https://blog.csdn.net/qq\_25430563/article/details/121704641)    &#x20;

[https://cloud.tencent.com/developer/article/1614353](https://cloud.tencent.com/developer/article/1614353)    &#x20;

[https://www.cnblogs.com/lcgbk/p/13281138.html](https://www.cnblogs.com/lcgbk/p/13281138.html)         &#x20;



### qt QObject::connect: Parentheses expected <a href="#articlecontentid" id="articlecontentid"></a>

原因：connect时槽函数忘记加()

解决：加上()即可



Reference：

[https://blog.csdn.net/lxj362343/article/details/106998445](https://blog.csdn.net/lxj362343/article/details/106998445)
