# QT 自定义控件-倒计时控件

## 开发流程

(1) 创建窗口控件，绘制背景，设置固定大小，在屏幕居中

创建窗口控件 window widget（直接新建一个C++类，继承QWidget；析构函数一般定义成虚函数）

自定义控件通常都需要自己去绘制，所以要重写paintEvent函数，然后我们在paintEvent函数里使用QPainter绘制，加载背景图片

```cpp
void WindowWidget::paintEvent(QPaintEvent *event)
{
    Q_UNUSED(event);

    QPainter painter(this);   // this表示在当前控件里paint
    painter.save();
    painter.drawPixmap(rect(), QPixmap(":/images/ImageResources/background.png"));
    painter.restore();
}
```



现在设置窗口控件为固定大小，并在屏幕居中；注释掉的代码只适用于单个显示器，但是现实中经常出现多显示器的场景，所以我们使用QScreen类来代替QDesktopWidget类；

```cpp
#define LANGCHERWIDGET_WIDTH 436
#define LANGCHERWIDGET_HEIGHT 775

WindowWidget::WindowWidget(QWidget* parent) : QWidget(parent)
{
    // 设置窗口固定大小
    setWindowFlags(windowFlags() & ~Qt::WindowMinMaxButtonsHint);
    setMinimumSize(LANGCHERWIDGET_WIDTH, LANGCHERWIDGET_HEIGHT);
    setMaximumSize(LANGCHERWIDGET_WIDTH, LANGCHERWIDGET_HEIGHT);
    setAutoFillBackground(true);

    // 窗口在屏幕居中
    // QDesktopWidget* desktop = QApplication::desktop();
    // int startX = (desktop->width() - LANGCHERWIDGET_WIDTH) / 2;
    // int startY = (desktop->height() - LANGCHERWIDGET_HEIGHT) / 2;

    QScreen *screen = QApplication::primaryScreen();
    QRect screenRect = screen->availableGeometry();
    int startX = (screenRect.width() - LANGCHERWIDGET_WIDTH) / 2;
    int startY = (screenRect.height() - LANGCHERWIDGET_HEIGHT) / 2;
    setGeometry(startX, startY, LANGCHERWIDGET_WIDTH, LANGCHERWIDGET_HEIGHT);
}
```



(2) 创建标尺控件，标尺头部控件；rulerheader控件绘制背景，设置固定大小；strechruler控件设置固定大小和颜色（颜色用来测试之后rulerheader的滑动）；把rulerheader控件放在strechruler控件前面，rulerheader控件放在windowwidget控件前面；启动程序的时候加载出所有控件



创建strechruler控件，rulerheader控件，也是定义类让它继承QWidget；绘制背景还是跟之前一样，重写paintEvent函数，加载图片；设置固定大小就是setGeometry；设置颜色需要使用QPalette

窗口控件前面有标尺控件，标尺控件前面有标尺头部控件（一般B控件在A控件的上面，我们就说B在A的前面）

标尺控件加载标尺头部控件：在StrechRuler类中加入一个成员RulerHeader类指针，具体操作就是StrechRuler的头文件中声明RulerHeader类，然后成员变量里添加RulerHeader类指针，然后在StrechRuler的源文件中初始化这个指针 `pRulerHeader = new RulerHeader(this);` this表明rulerheader的父控件是当前控件

窗口控件加载标尺控件：一般窗口控件可能会加载多个控件，所以我们把加载控件的操作封装在一个loadFrontWidgets函数里；具体操作也是WindowWidget.h中声明要加载的控件类，成员变量里添加要加载的控件类指针，之后loadFrontWidgets函数里new一个要加载的控件类对象初始化指针；

```cpp
// rulerheader
class RulerHeader : public QWidget
{
    Q_OBJECT
public:
    explicit RulerHeader(QWidget *parent = nullptr);
    ~RulerHeader();

protected:
    void paintEvent(QPaintEvent* event);
};


#define RULERHEADER_WIDTH 82
#define RULERHEADER_HEIGHT 91

RulerHeader::RulerHeader(QWidget *parent)
    : QWidget{parent}
{
    setGeometry(0, 0, RULERHEADER_WIDTH, RULERHEADER_HEIGHT); // 设置大小
}

void RulerHeader::paintEvent(QPaintEvent* event)
{
    Q_UNUSED(event);
    QPainter painter(this);
    painter.setRenderHint(QPainter::Antialiasing, true);
    
    // 绘制背景
    painter.save();
    painter.drawPixmap(rect(), QPixmap(":/images/ImageResources/ruler_head.png"));
    painter.restore();
}



// strechruler
class RulerHeader;  // 向前声明 而不直接include

class StrechRuler : public QWidget
{
    Q_OBJECT
public:
    explicit StrechRuler(QWidget *parent = nullptr);
    virtual ~StrechRuler();
    
private:
    RulerHeader* pRulerHeader;  // 定义rulerheader控件类指针
};


#define RULERWIDGET_STARTX 351
#define RULERWIDGET_STARTY 100

#define RULERWIDGET_WIDTH 82
#define RULERWIDGET_HEIGHT 574

StrechRuler::StrechRuler(QWidget *parent)
    : QWidget{parent}
{
    // 设置大小
    setGeometry(RULERWIDGET_STARTX, RULERWIDGET_STARTY, RULERWIDGET_WIDTH, RULERWIDGET_HEIGHT);
    
    // 设置背景颜色
    setAutoFillBackground(true);
    QPalette palette;
    palette.setColor(QPalette::Background, Qt::green);
    setPalette(palette);
    
    // 加载rulerheader控件
    pRulerHeader = new RulerHeader(this);
}

StrechRuler::~StrechRuler()
{
    if (pRulerHeader) {
        delete pRulerHeader;
        pRulerHeader = nullptr;
    }
}


// window widget
class StrechRuler;

class WindowWidget : public QWidget
{
    Q_OBJECT
public:
    explicit WindowWidget(QWidget* parent = nullptr);
    virtual ~WindowWidget();
    
private:
    void loadFrontWidgets();

private:
    StrechRuler* pStrechRuler;
};

WindowWidget::WindowWidget(QWidget* parent) : QWidget(parent)
{
    loadFrontWidgets();
}

WindowWidget::~WindowWidget()
{
    if (pStrechRuler) {
        delete pStrechRuler;
        pStrechRuler = nullptr;
    }
}

void WindowWidget::loadFrontWidgets()
{
    pStrechRuler = new StrechRuler(this);
}
```



(3) rulerheader控件，鼠标左键按住后可以上下拖动

为了实现鼠标左键按住rulerheader后拖动，<mark style="background-color:orange;">首先需要重写rulerhader的鼠标事件函数</mark>（mousePressEvent, mouseMoveEvent, mouseReleaseEvent），<mark style="background-color:orange;">核心就是要得到mouse move时，当前鼠标位置相对于鼠标点击时位置的偏移量</mark>，鼠标点击时的位置其实就是mose move前的位置，一般命名last\_mouse\_position;



<mark style="background-color:orange;">然后鼠标按住rulerheader拖动的本质就是，我们把rulerheader中鼠标偏移量传递给父控件strechruler，然后strechruler根据rulerheader的当前位置和鼠标偏移量去移动rulerheader；</mark>

也就是说鼠标拖动rulerheader时，我们需要把鼠标位置偏移量传给父控件，然后父控件根据rulerheader的当前位置和传过来的偏移量去移动rulerheader；以及在鼠标放开rulerheader时通知父控件去记录rulerheader的当前位置；



这种控件之间的通信，我们采用信号槽来实现；一般信号和槽都是一一对应的，一个信号对应一个槽函数，然后槽函数的名字一般都是信号函数的名字前加on去掉signal；

鼠标按住rulerheader拖动时，发出信号rulerHeaderMoveSignal(position.y())将鼠标纵坐标偏移量传给父控件strechruler，strechruler收到信号后调用槽函数onRulerHeaderMove，移动rulerheader；

鼠标放开rulerheader时，发出信号rulerHeaderMoveDoneSignal()通知父控件strechruler，strechruler收到信号后调用槽函数onRulerHeaderMoveDone，记录rulerheader的位置；

```cpp
// rulerheader 子控件
class RulerHeader : public QWidget
{
    Q_OBJECT

signals:
    void rulerHeaderMoveSignal(int posY);
    void rulerHeaderMoveDoneSignal();

protected:
    void mousePressEvent(QMouseEvent* event);
    void mouseMoveEvent(QMouseEvent* event);
    void mouseReleaseEvent(QMouseEvent* event);

private:
    QPoint last_mouse_position;
};


void RulerHeader::mousePressEvent(QMouseEvent* event)
{
    if (event->button() == Qt::LeftButton)
    {
        last_mouse_position = event->globalPos();  // 记录鼠标点击时的位置
        // qDebug() << "mouse press";
    }
}

void RulerHeader::mouseMoveEvent(QMouseEvent* event)
{
    if (event->buttons() & Qt::LeftButton)
    {
        QPoint position = event->globalPos() - last_mouse_position;  // 计算鼠标偏移量
        emit rulerHeaderMoveSignal(position.y());   // 传递鼠标偏移量给父控件strechruler
        // qDebug() << "mouse move";
    }
}

void RulerHeader::mouseReleaseEvent(QMouseEvent* event)
{
    Q_UNUSED(event);
    emit rulerHeaderMoveDoneSignal();   // 通知父控件strechruler
    // qDebug() << "mouse release";
}



// strechruler 父控件
class RulerHeader;  // 向前声明 而不直接include
class StrechRuler : public QWidget
{
    Q_OBJECT
public:
    explicit StrechRuler(QWidget *parent = nullptr);
    virtual ~StrechRuler();

private slots:
    void onRulerHeaderMove(int aValue);
    void onRulerHeaderMoveDone();
    
private:
    RulerHeader* pRulerHeader;
    QPoint  originalPostion;   // 子控件rulerheader的原始位置
    int currentPosY;           // 子控件rulerheader的当前位置的y值
};


StrechRuler::StrechRuler(QWidget *parent)
    : QWidget{parent}
{
    pRulerHeader = new RulerHeader(this);
    // 父控件中连接信号槽
    connect(pRulerHeader, SIGNAL(rulerHeaderMoveSignal(int)), this, SLOT(onRulerHeaderMove(int)));
    connect(pRulerHeader, SIGNAL(rulerHeaderMoveDoneSignal()), this, SLOT(onRulerHeaderMoveDone()));
    
    // 初始状态下，rulerheader的原始位置和当前位置的y值
    originalPostion = pRulerHeader->pos();
    currentPosY = 0;   // 上下拖动只会变动y值
}

void StrechRuler::onRulerHeaderMove(int aValue)
{
    // qDebug() << "PosY:" << aValue;
    if(pRulerHeader == nullptr) {
        return;
    }

    int realPosY = currentPosY + aValue;   // 计算子控件rulerheader移动后的位置 = 当前位置 + 偏移量
    if ((realPosY <= RULER_MAX_HEGIHT) && (realPosY >= 0)) {
        pRulerHeader->move(originalPostion.rx(), realPosY);   // 子控件rulerheader移动到新位置
    }
}

void StrechRuler::onRulerHeaderMoveDone()
{
    currentPosY = pRulerHeader->pos().y();   // 记录子控件rulerheader的当前位置
}
```



(4) 鼠标按住rulerheader控件拖动的时候，strechruler控件实时绘制对应的尺子

简单来说，我们想要实现的效果是，拖动rulerheader的时候有对应的尺子拉长缩短；

这个尺子其实是strechruler的一部分，我们重写paintEvent去绘制这个尺子；paintEvent函数里，我们使用QPainter去绘制；

首先绘制尺子的轮廓使用drawRect，设置边框颜色使用setPen，设置尺子内部的颜色使用setBrush；

之后就是绘制尺上的刻度线，刻度线分为两种短线和长线，所以使用drawLine去绘制刻度线；然后使用setPen设置度线的颜色；绘制刻度线的逻辑很简单，先计算当前短线和长线所需要绘制的个数，然后就是循环里逐个绘制；

rulerheader拖动时，strechruler的尺子就会改变（意味着重新绘制），所以我们要在onRulerHeaderMove槽函数里最后加上repaint()；此时鼠标按住rulerheader控件拖动的时候，strechruler就会实时绘制出对应的尺子；

```cpp
// strechruler
class StrechRuler : public QWidget
{
protected:
    void paintEvent(QPaintEvent* event);
};

void StrechRuler::onRulerHeaderMove(int aValue)
{
    repaint();
}

void StrechRuler::paintEvent(QPaintEvent* event)
{
    Q_UNUSED(event);

    QPainter painter(this);
    painter.setRenderHint(QPainter::Antialiasing, true);  // 反锯齿
    
    // 绘制尺子
    painter.save();
    painter.setPen(QPen(QColor(127, 159, 124),1));   // 绘制尺子边框的颜色
    painter.setBrush(QColor(127, 159, 124));         // 填充尺子内部的颜色
    painter.drawRect(15, 3, 52, pRulerHeader->pos().y());   // 绘制尺子轮廓
    painter.restore();

    // 绘制尺子上的刻度线
    int sScale = 5;      // 短线间距
    int lScale = 25;     // 长线间距
    int currentRulerHeight = pRulerHeader->pos().y();   // 当前rulerhader的y值

    int sScale_count = currentRulerHeight / sScale + 1;   // 短线个数
    int lScale_count = currentRulerHeight / lScale + 1;   // 长线个数

    painter.setPen(QPen(QColor(255, 255, 255), 1));       // 设置刻度线的颜色
    
    // 绘制短刻度线
    for (int i = 0; i < sScale_count; i ++) {
        if (i % 5 != 0) {
            painter.drawLine(QPoint(67 - 5, 5*i), QPoint(67, 5 * i));
        }
    }
    
    // 绘制长刻度线
    for (int i = 0; i < lScale_count; i ++) {
        painter.drawLine(QPoint(67 - 10, 25 * i), QPoint(67, 25 * i));
    }
}
```



(5) 创建表盘控件，设置表盘控件的大小，绘制表盘背景；在表盘控件中绘制指针；最后表盘控件加载到窗口控件中；

首先创建表盘控件dialock，还是基本操作，setGeometry设置dialock控件的大小，然后重写paintEvent函数，利用drawPixmap设置dalock控件的背景；

之后我们要在表盘控件中绘制指针；

第一步 先将绘制点平移到dialock控件的正中间；第二步 找到该点 左边半个指针的宽度，上面（一个指针的长度 - 指针圈的一半）的位置；这里才是最终的绘制点；然后设置旋转角度；第三步 绘制指针，设置指针背景

最后我们要把表盘控件添加到窗口控件中，跟之前一样的操作；窗口控件中添加成员表盘控件类指针，然后在loadWidgest里new一个表盘控件对象初始化这个指针，同时指定窗口控件为表盘指针控件的父控件；

```cpp
// dialock
class DialClock : public QWidget
{
    Q_OBJECT
public:
    explicit DialClock(QWidget *parent = nullptr);
    virtual ~DialClock();

protected:
    void paintEvent(QPaintEvent* event);
};


#define DIALCLOCK_STARTX 75
#define DIALCLOCK_STARTY 188
#define DIALCLOCK_WIDTH 285
#define DIALCLOCK_HEIGHT 285

#define NEEDLE_WIDTH 26
#define NEEDLE_HEIGHT 90


DialClock::DialClock(QWidget *parent)
    : QWidget{parent}
{
    setGeometry(DIALCLOCK_STARTX, DIALCLOCK_STARTY, DIALCLOCK_WIDTH, DIALCLOCK_HEIGHT);
    setStyleSheet("background-color: rgba(0, 0, 0, 0)");
}

void DialClock::paintEvent(QPaintEvent *event)
{
    Q_UNUSED(event);
    QPainter painter(this);
    painter.setRenderHint(QPainter::Antialiasing, true);   // 设置反锯齿
    painter.setRenderHint(QPainter::SmoothPixmapTransform, true);  // 设置平滑处理
    
    // 绘制表盘背景
    painter.save();
    painter.drawPixmap(rect(), QPixmap(":/images/ImageResources/dial.png"));  // 绘制表盘背景
    painter.restore();
    
    // 绘制指针
    painter.save();
    painter.translate(width()/ 2, height() / 2);    // 平移到中心点
    painter.rotate(-45);                    // 旋转指针
    painter.drawPixmap(-13, -78, NEEDLE_WIDTH, NEEDLE_HEIGHT, QPixmap(":/images/ImageResources/needle.png"));
    painter.restore();
}



// window widget
class WindowWidget : public QWidget
{
    Q_OBJECT
explicit WindowWidget(QWidget* parent = nullptr);
virtual ~WindowWidget();

private:
    void loadFrontWidgets();

private:
    DialClock* pDialClock;
};

WindowWidget::WindowWidget(QWidget* parent) : QWidget(parent)
{
    loadFrontWidgets();
}

void WindowWidget::loadFrontWidgets()
{
    pDialClock = new DialClock(this);  // 表盘控件指定当前控件为父控件
}

WindowWidget::~WindowWidget()
{
    if (pDialClock) {
        delete pDialClock;
        pDialClock = nullptr;
    }
}
```



(6) strechruler控件的尺子拉动时，diaclock控件的指针进行对应旋转

很明显我们需要 strechruler控件 和 diaclock控件 进行通信，具体来说就是strechruler的尺子长度变化时，发送信号 rulerstrechSignal(int) 将长度比率传给strechruler控件，strechruler控件收到信号调用槽函数setupClockTime(int)设置当前的rotateValue，然后repaint重新绘制指针；

其中由于strechruler控件和diaclock控件不是父子控件关系，但是它们有共同的父控件window widget；所以我们将槽函数声明为public，然后在window widget中connect信号和槽；

```cpp
// strechruler
class StrechRuler : public QWidget
{
signals:
    void rulerStrechSignal(float value);
}

void StrechRuler::onRulerHeaderMove(int aValue)
{
    float dialRatio = pRulerHeader->pos().y() / 486.0;
    emit rulerStrechSignal(dialRatio);
}

// diaclock
class DialClock : public QWidget
{

public slots:
    void setupClockTime(float value);

private:
    float rotateValue;
}

DialClock::DialClock(QWidget *parent) : QWidget{parent}
{
    rotateValue = 0.0;   // 设置旋转角度的初始值为0
}

void DialClock::setupClockTime(float value)
{
    rotateValue = 360 * value;   // strechruler传过来的是比率，*360得到旋转角度
    repaint();                   // 旋转角度发生变化后，重新绘制表盘控件来重新绘制表盘控件中的指针
}

void DialClock::paintEvent(QPaintEvent *event)
{
    painter.rotate(rotateValue);   // 根据成员变量rotatevalue去旋转
}


// window widget
void WindowWidget::loadFrontWidgets()
{
    // 连接strechruler控件中的信号和diaclock中的槽函数
    // 注意信号都是public的，槽函数可以随意限定，这里在diaclock中的父控件中connect，所以diaclock的槽函数要设为public
    connect(pStrechRuler, SIGNAL(rulerStrechSignal(float)), pDialClock, SLOT(setupClockTime(float)));
}
```



