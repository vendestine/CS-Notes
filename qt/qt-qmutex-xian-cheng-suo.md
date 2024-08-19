# QT QMutex线程锁

QMutex是Qt中用于实现互斥锁的累，用于保护共享资源免受并发访问的影响。互斥锁（Mutex）是一种同步机制，用于确保在任意时刻只有一个线程能够访问共享资源。

创建QMutex对象：你需要创建一个QMutex对象来管理共享资源的访问。通常，这个对象会在类的私有部分作为成员变量存在。

锁定（lock）：在访问共享资源之前，你需要锁定互斥锁。通过调用QMutex对象的lock函数可以实现这一点。lock() unlock()

尝试锁定：tryLock函数可以尝试锁定互斥锁，如果锁定成功返回true，否则返回false。这对于避免线程阻塞很有用。

递归锁定：如果同一个线程需要多次锁定同一个互斥锁，可以使用QMutex的recursive构造函数来创建递归锁。这样，同一个线程可以多次锁定同一个互斥锁，而不会思索。

为了确保在发生异常或提前返回时互斥锁总是被释放，可以使用QMutexLocker对象，它是QMutex的一个辅助类：





示例：

```cpp
// mainwindow.h
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

private:
    Ui::MainWindow *ui;
    Thread* m_pThread1;
    Thread* m_pThread2;
    SharedResource* m_pShareRes;
};
#endif // MAINWINDOW_H



// mainwindow.cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QPushButton>
#include <QDebug>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);

    m_pShareRes = new SharedResource();

    m_pThread1 = new Thread(m_pShareRes);
    m_pThread1->start();

    m_pThread2 = new Thread(m_pShareRes);
    m_pThread2->start();
}

MainWindow::~MainWindow()
{
    if (m_pThread1 != nullptr) {
        m_pThread1->stopThreadRunning();
        m_pThread1->quit();
        m_pThread1->wait();
        m_pThread1->deleteLater();
    }

    if (m_pThread2 != nullptr) {
        m_pThread2->stopThreadRunning();
        m_pThread2->quit();
        m_pThread2->wait();
        m_pThread2->deleteLater();
    }

    if (m_pShareRes != nullptr) {
        delete m_pShareRes;
    }
    delete ui;
}


// thread.h
#ifndef THREAD_H
#define THREAD_H
#include <QObject>
#include <QThread>
#include <QMutex>
#include <QMutexLocker>
#include <QDebug>

class SharedResource
{
public:
    SharedResource(int value_ = 0): value(value_){}

    void modifyResource()
    {
        // m_mutex.lock();
        QMutexLocker locker(&m_mutex);
        for (int i = 0; i < 20; i ++) {
            qDebug() << "Thread ID:" << QThread::currentThread() << "value:" << ++value;
        }
        // m_mutex.unlock();
    }

private:
    int value;  // 共享资源
    QMutex m_mutex;  // 互斥锁
};

class Thread : public QThread
{
    Q_OBJECT
public:
    explicit Thread(SharedResource* res, QObject *parent = nullptr);
    ~Thread();
    void stopThreadRunning();

protected:
    void run();

private:
    bool isRunning;
    SharedResource* pResource;
};

#endif // THREAD_H


// thread.cpp
#include "thread.h"
#include <QDebug>

Thread::Thread(SharedResource* res, QObject *parent)
    : QThread{parent},
    isRunning(true),
    pResource(res)

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
        msleep(1000);
        pResource->modifyResource();
    }
    qDebug() << "==========Thread exit...============";
}


```
