# Untitled

## QT信号和槽

### 入门：使用方法

> 案例1，展示了QT种信号和槽的多种使用方法

\


#### 案例1：实现一个简单计数器

需求：有多个按钮，每个按钮点击一次，计数数值+1\
首先设计一个界面，counter界面，所以类取名counter，生成counter.h和counter.cpp，counter.ui文件；一般一个界面对应三个文件；我们ui文件里设计界面，C++文件里实现界面的逻辑和数据交互；\
现在我们需要实现按钮点击，计数值 + 1；这里可以直接使用QT的预定义信号clicked()，使用QT的信号槽，我们有很多种写法\


1. 使用QT Creator中的Design，选中发出信号的对象，然后go to slot，实现槽（函数）

\
\


<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=MTRhMzgyNzQ0OGJiNjhlYzM4ODM5NWU4ZjY1MTlmMTJfcXFXTTdPbnA1S3ZFUzg3aklrWHNlS1FJSDUxZm9EZjFfVG9rZW46WFRVY2JqMjB1b2Q0bUJ4aWJJeGM4UWxSbmVnXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=NjA1MDYwYTQyYjVhMjRmZmJlOTg1MjhmZWJlODg5ZTJfaThoTmpySDNNc1ltZ2lkS2E4dTRYT01DV1dEekJVVkdfVG9rZW46Um4xeWJaSVFhb0Q2djV4ck54aWM2bGZubnpiXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

2. 使用connet，连接信号和槽；QT4采用SIGNAL和SLOT宏，QT5直接使用函数指针

QT4使用宏：clickedButton\_1Qt5使用函数指针：clickedButton\_2\


<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=NjY3ZTM4OGFhNTMxZjk4MmY5Mjg1MzYzM2VlYmZlN2JfYTVKV0hObW4zZmYyZ2Q0Snk5T1FqYmNUT0FWZWJNMHVfVG9rZW46VFdVVmJSR1Vzb0ljeHh4T1JCaWNhRzVTbmNnXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

3. 使用connect，连接信号和槽；connect to functors or C++11 lambdas

connect to C++11 lambda：clickedButton\_3\
\


<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=N2IxZjZmYTI4N2Q5NzRlOTljZDdmZjUzYWM5NGMxMGJfWXJmYjZ5VWFmS0tjbG5pT25CTDhYWE5uYXRsYnVHSG5fVG9rZW46TFRydWJKY0FXb2lhSkN4UklBZmNEVGpqblVnXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

> 这篇文章，对于信号槽的多种写法总结的很好，也可以那这篇文章进行复习[https://www.cnblogs.com/jfzhu/p/13501678.html](https://www.cnblogs.com/jfzhu/p/13501678.html)

\
\


### 进阶：原理和注意事项

\
信号和槽的本质：信号和槽的本质都是函数，我们可以使用QT预定义的信号函数和槽函数，也可也自定义信号函数和槽函数；信号是公共访问的函数，在头文件中的signals关键字后声明，实现是由moc自动实现；而槽是成员函数，在头文件中的slots关键字后声明，需要自己实现，有类的访问限制；\
\
\
这里转载一张思维导图，对于QT信号和槽总结的还是挺全面的\
\


<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmFjMjlhZGFlMjYyMzViOTU3OWViYjc1MjU4ODZlNDNfSTNYU1ptSlFqRVJNemVYdFRYYUl0TG1BNFFJR0RjMDZfVG9rZW46REpSY2JPcVU2b1BpTzV4aGNKM2M4VkhybkVjXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2VjYzkzYjA4MTA2Mzc1ZTY0OWJjNzkwYzA5NjcxNDNfS015Nmk3OG5mcFpVdUlPRmJqbE1aNFFWMVFrSlFOQVRfVG9rZW46RWhjb2JkZGRKb1F3REJ4OEQyMGNwcFJSbmJiXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

<figure><img src="https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=ZWI4Yjc1MGEyZGI2NWE0ZWUwNjA5ZTMyMzgwYzdkYTZfQlVVR05KUmEyb2RocDJDOWxLWkRCeUQ0TXQwaHRyYk9fVG9rZW46STM1aWJaNDV5b292M2p4Zk9PZmN6SW1qbnBnXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA" alt=""><figcaption></figcaption></figure>

> 案例2，展示自定义信号和槽的使用场景，以及使用方式

#### 案例2：实现一个进阶计数器

需求：有三个按钮，add按钮点击，计数 + 1，sub按钮点击，计数 -1，reset按钮点击，计数复位；如果计数 = 10，需要弹窗通知；按钮点击，我们可以使用预定义信号clicked()，计数 = 10，弹窗通知，我们可以自定义弹窗信号，然后再实现对应的槽；\
counter.h

```C++
#ifndef COUNTER_H
#define COUNTER_H

#include <QWidget>

QT_BEGIN_NAMESPACE
namespace Ui {
class counter;
}
QT_END_NAMESPACE

class counter : public QWidget
{
    Q_OBJECT

public:
    counter(QWidget *parent = nullptr);
    ~counter();

signals:
    void popWindow(int num);


private slots:
    void onAddButtonClicked();
    void onSubButtonClikced();
    void onPopWindow(int num);

private:
    Ui::counter *ui;
};
#endif // COUNTER_H
```

\
counter.cpp

```C++
#include "counter.h"
#include "ui_counter.h"
#include <QMessageBox>
#include <QDebug>

counter::counter(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::counter)
{
    ui->setupUi(this);

    connect(ui->addButton, SIGNAL(clicked()), this, SLOT(onAddButtonClicked()));
    connect(ui->subButton, &QPushButton::clicked, this, &counter::onSubButtonClikced);
    connect(ui->resetButton, &QPushButton::clicked, this, = {
        int currentValue = ui->numberLabel->text().toInt();
        currentValue = 0;
        ui->numberLabel->setText(QString::number(currentValue));
    });


    connect(this, &counter::popWindow, this, &counter::onPopWindow);
}

counter::~counter()
{
    delete ui;
}

void counter::onAddButtonClicked()
{
    int currentValue = ui->numberLabel->text().toInt();
    ++ currentValue;
    ui->numberLabel->setText(QString::number(currentValue));
    if (currentValue == 10) emit popWindow(currentValue);
}

void counter::onSubButtonClikced()
{
    int currentValue = ui->numberLabel->text().toInt();
    -- currentValue;
    ui->numberLabel->setText(QString::number(currentValue));
    if (currentValue == 10) emit popWindow(currentValue);
}


void counter::onPopWindow(int num)
{
    QMessageBox messageBox;
    messageBox.setWindowTitle("Notification");
    messageBox.setText("You have counted for 10!");
    messageBox.setStandardButtons(QMessageBox::Ok | QMessageBox::Cancel);
    messageBox.setIcon(QMessageBox::Information);
    messageBox.exec();
    qDebug() << "clicked button times " << num << Qt::endl;
}
```

\
main.cpp

```C++
#include "counter.h"
#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    counter w;
    w.show();
    return a.exec();
}
```

\
界面：案例演示：![](https://kit8qrfi27h.feishu.cn/space/api/box/stream/download/asynccode/?code=YWU3NWM2MmY0MDcyYzg0YzViMzRiY2M5YWIwNTdiODRfQ1VFMzhaM09RU0o3dFoyTnhMb2VPZXhjTE9Fak9lSW5fVG9rZW46UUFCZmJyZzFjb0lCQ2V4V1V2ZGNrSmhHblFmXzE3MjI3OTYyNzk6MTcyMjc5OTg3OV9WNA)\
\
Reference:

1. [Signals & Slots](https://doc.qt.io/qt-6/signalsandslots.html)
2. [Qt 之 emit、signals、slot的使用 - schips - 博客园](https://www.cnblogs.com/schips/p/12537360.html)
3. [Qt信号与槽使用方法最完整总结 - AI观星台 - 博客园](https://www.cnblogs.com/jfzhu/p/13501678.html)
4. [QT 信号与槽详解\_在qt中使用信号和槽函数的理解-CSDN博客](https://blog.csdn.net/sazass/article/details/104903353)
5. [Qt - 一文理解信号槽机制(万字剖析整理)\_qt信号槽机制-CSDN博客](https://blog.csdn.net/weixin\_40774605/article/details/109342536)
6. [QT的信号槽机制(slot与signal) - 掘金](https://juejin.cn/post/6847902223691677703)

\
