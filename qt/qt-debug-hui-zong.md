# QT Debug汇总

### **QMetaObject::connectSlotsByName: No matching signal for on\_clickedButton\_clicked()**

报错的原因：有`on_<objectName>_<signalName>`形式的函数并且找不到对象名为objectName的对象

解决办法：更改函数名，避免用`on_<objectName>_<signalName>`的槽函数，显示用connect连接信号和槽\


Reference：

[QMetaObject::connectSlotsByName: No matching signal for XXX 原理探究 - 康宇PL - 博客园](https://www.cnblogs.com/kangyupl/p/13525261.html)

[QMetaObject::connectSlotsByName: No matching signal for问题的解决方法 - etcjd - 博客园](https://www.cnblogs.com/chen-diary/p/6927885.html)



## Qt直接运行生成的exe文件提示“缺少Qt5Core.dll”的解决办法 <a href="#articlecontentid" id="articlecontentid"></a>

[https://blog.csdn.net/liubing8609/article/details/87474900](https://blog.csdn.net/liubing8609/article/details/87474900)

