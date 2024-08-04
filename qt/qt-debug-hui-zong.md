# QT Debug汇总

QMetaObject::connectSlotsByName: No matching signal for on\_clickedButton\_clicked()

报错的原因：有`on_<objectName>_<signalName>`形式的函数并且找不到对象名为objectName的对象

解决办法：更改函数名，避免用`on_<objectName>_<signalName>`的槽函数，显示用connect连接信号和槽



Reference：

{% embed url="https://www.cnblogs.com/kangyupl/p/13525261.html" %}

{% embed url="https://www.cnblogs.com/chen-diary/p/6927885.html" %}
