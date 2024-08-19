# QT QThread多线程

QThread是QT框架中用于支持多线程编程的类。它提供了一个面向对象的接口，使得创建和管理线程变得更加容易。

通常，你需要创建一个继承自QThread的子类，并在其中实现线程的运行逻辑。重写run函数，它包含线程将执行的代码。

创建线程对象：在应用程序中创建QThread的实例，并在需要的时候启动线程。通常，你会创建线程对象，并使用start函数启动线程。

线程运行逻辑：run函数定义了线程的运行逻辑。在这个函数中，你可以编写线程需要执行的代码。线程会在start被调用后自动运行run函数。

信号和槽连接：由于QT多线程不能直接访问GUI元素，因此你可能需要使用信号槽机制来在不同线程之间进行通信。确保在正确的线程上处理GUI相关的操作。

线程安全：在多个线程同时访问共享数据时，确保使用适当的同步机制，以避免竞争条件和数据不一致性。

结束线程：要结束线程，你可以调用quit或exit函数，并等待线程完成。也可以使用terminate函数，但它可能导致资源卸扣，因此不推荐使用。





QT 多线程案例:

mainwindow.h

```cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <thread.h>

QT_BEGIN_NAMESPACE
namespace Ui {
class MainWindow;
}
QT_END_NAMESPACE

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

public slots:
    void onButtonClicked();
    void onEmitSignalUpdate(int num);

private:
    Ui::MainWindow *ui;
    Thread* m_pThread;
};
#endif // MAINWINDOW_H

```



mainwindow.cpp

```cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QPushButton>
#include <QDebug>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    connect(ui->clickedButton, &QPushButton::clicked, this, &MainWindow::onButtonClicked);

    m_pThread = new Thread();
    m_pThread->start();

    // 一定要放在start后面
    connect(m_pThread, &Thread::updateThreadSignal, this, &MainWindow::onEmitSignalUpdate);
}

MainWindow::~MainWindow()
{
    if (m_pThread != nullptr) {
        m_pThread->stopThreadRunning();
        m_pThread->quit();
        m_pThread->wait();
        m_pThread->deleteLater();
    }
    delete ui;
}


void MainWindow::onButtonClicked()
{
    qDebug() <<"=============================onButtonClicked ===================================";
    m_pThread->stopThreadRunning();
}


void MainWindow::onEmitSignalUpdate(int num)
{
    qDebug() <<"=============================onEmitSignalUpdate ===================================" << num;
}



```



thread.h

```cpp
#ifndef THREAD_H
#define THREAD_H
#include <QObject>
#include <QThread>

class Thread : public QThread
{
    Q_OBJECT
public:
    explicit Thread(QObject *parent = nullptr);
    ~Thread();
    void stopThreadRunning();

signals:
    void updateThreadSignal(int num);

protected:
    void run();

private:
    bool isRunning;
    int nCount;
};

#endif // THREAD_H

```



thread.cpp

```cpp
#include "thread.h"
#include <QDebug>

Thread::Thread(QObject *parent)
    : QThread{parent},
    isRunning(true),
    nCount(0)
{

}

Thread::~Thread()
{

}

void Thread::stopThreadRunning()
{
    isRunning = false;
}

void Thread::run()
{
    while(isRunning) {
        ++nCount;
        emit updateThreadSignal(nCount);
        msleep(1000);
    }
    qDebug() << "==========Thread exit...============";
}

```
