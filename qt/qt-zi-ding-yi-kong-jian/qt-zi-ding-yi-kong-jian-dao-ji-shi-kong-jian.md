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



(7) 创建一个QLabel控件用来显示倒计时时间，创建两个QPushButton按钮，一个用来开始倒计时，一个用来复位

这三个控件不需要自定义，因为QT内部已经有比较贴合的控件了；这三个控件都是加载在window widget上；

```cpp
// window widget
class WindowWidget : public QWidget
{
private:
    void loadFrontWidgets();
    
private:
    QLabel* pTimeLabel;
    QPushButton* pPlayButton;
    QPushButton* pResetButton;
}


void WindowWidget::loadFrontWidgets()
{
    // 设置timelabal的字体
    QFont font;
    font.setFamily("PingFang TC");
    font.setWeight(25);
    font.setPixelSize(35);
    
    // 创建一个timelabel控件
    pTimeLabel = new QLabel(this);
    pTimeLabel->setGeometry(138,525,160,60);
    pTimeLabel->setFont(font);
    pTimeLabel->setStyleSheet("background: transparent; color: 3d16b5d");
    pTimeLabel->setAlignment(Qt::AlignCenter);
    pTimeLabel->setText("00:00");

    
    // 创建一个playbutton控件
    pPlayButton = new QPushButton(this);
    pPlayButton->setGeometry(111,630,TOOLBUTTON_WIDTH,TOOLBUTTON_WIDTH);
    pPlayButton->setIcon(QIcon(":/images/ImageResources/play.png"));
    pPlayButton->setIconSize(QSize(TOOLBUTTON_WIDTH, TOOLBUTTON_HEIGHT));
    pPlayButton->setAutoFillBackground(true);
    pPlayButton->setFlat( true );
    pPlayButton->setStyleSheet("QPushButton{background: transparent;}");
    
    // 创建一个resetbutton控件
    pResetButton = new QPushButton(this);
    pResetButton->setGeometry(251,630,TOOLBUTTON_WIDTH,TOOLBUTTON_WIDTH);
    pResetButton->setIcon(QIcon(":/images/ImageResources/reset.png"));
    pResetButton->setIconSize(QSize(TOOLBUTTON_WIDTH, TOOLBUTTON_HEIGHT));
    pResetButton->setAutoFillBackground(true);
    pResetButton->setFlat( true );
    pResetButton->setStyleSheet("QPushButton{background: transparent;}");
}
```



(8) 停止拖动rulerheader后，设置timelabal里的时间

很明显，我们需要strechruler控件和timelabel的父控件（window widget)进行通信；具体来说在strechruler的槽函数onRulerHeaderMoveDone()中，发出信号rulerStrechDoneSignal(dialRatio)将当前尺子的比例传给window widget，window widget接收到信号后调用槽函数onRulerStrechDone(float value)，该槽函数中我们使用calculateFormateTimeWithSeconds函数去计算格式化后的时间；然后渲染到QLabel中

```cpp
// strechruler
class StrechRuler : public QWidget
{
signals:
    void rulerStrechDoneSignal(float value);

private slots:
    void onRulerHeaderMoveDone();
}

void StrechRuler::onRulerHeaderMoveDone()
{
    currentPosY = pRulerHeader->pos().y();
    float dialRatio = pRulerHeader->pos().y() / 486.0;
    emit rulerStrechDoneSignal(dialRatio);
}


// window widget
class WindowWidget : public QWidget
{
private:
    void loadFrontWidgets();
    QString calculateFormatTimeWithSeconds(int seconds);

private slots:
    void onRulerStrechDone(float value);
}

void WindowWidget::loadFrontWidgets()
{
    connect(pStrechRuler, SIGNAL(rulerStrechDoneSignal(float)),this, SLOT(onRulerStrechDone(float)));
}

void WindowWidget::onRulerStrechDone(float value)
{
    int mSeconds = (int)3600 * value;
    QString formatTime = calculateFormatTimeWithSeconds(mSeconds);
    pTimeLabel->setText(formatTime);
}


QString WindowWidget::calculateFormatTimeWithSeconds(int seconds)
{
    int tMinutes = seconds / 60;
    int tSeconds = seconds - tMinutes * 60;

    QString strMinutes = QString("%1").arg(tMinutes);
    QString strSeconds = QString("%1").arg(tSeconds);

    if ((tMinutes < 10) && (tMinutes >= 0)) {
        strMinutes = QString("0%1").arg(tMinutes);
    }

    if ((tSeconds < 10) && (tSeconds >= 0)) {
        strSeconds = QString("0%1").arg(tSeconds);
    }

    QString formatTime = QString("%1: %2").arg(strMinutes).arg(strSeconds);

    return formatTime;
}
```



(8) 加入倒计时功能

加入倒计时功能，首先需要加入定时器；

在window widget里使用定时器，我们有固定的写法；创建一个QTimer指针，定义一个isTimerPlaying变量记录定时器是否在运行，然后在window widget的构造函数里，初始化isTimerPlaying，new一个QTimer对象去初始化QTimer指针；最后connect 定时器的timeout信号和处理函数

```cpp
class WindowWidget : public QWidget
{
    bool    isTimerPlaying;
    QTimer* pCountDownTimer;
}

WindowWidget::WindowWidget(QWidget* parent) : QWidget(parent)
{
    isTimerPlaying = false;
    pCountDownTimer = new QTimer(this);
    connect(pCountDownTimer, SIGNAL(timeout()), this, SLOT(onCountDownTimerProcess()));
}
```



然后，start按钮 点中之后，对应的槽函数里开启定时器，改变isTimerPlaying的状态，设置playbutton的背景为pause；start按钮取消点中之后，对应的槽函数里关闭定时器，改变isTimerPlaying的状态，最后playbutton的背景为play；

reset按钮点击之后，定时器复位（isTimerPlaying = false, stop())，playbutton复位（背景图片复位），当前秒数复位，timelabel的显示时间复位，表盘指针复位（调用dialclock的槽函数setupClockTime将指针恢复原位），rulerheader复位（调用strechruler的public函数setRulerReset将rulerheader移动到原点）

```cpp
class WindowWidget : public QWidget
{
private slots:
    void onPlayButtonClicked();
    void onResetButtonClicked();
}

void WindowWidget::loadFrontWidgets()
{
    connect(pPlayButton,SIGNAL(clicked(bool)),SLOT(onPlayButtonClicked()));
    connect(pResetButton,SIGNAL(clicked(bool)),SLOT(onResetButtonClicked()));
}


void WindowWidget::onPlayButtonClicked()
{
    if(isTimerPlaying == true)
    {
        if(pCountDownTimer->isActive()){
            pCountDownTimer->stop();
            isTimerPlaying = false;
        }
        pPlayButton->setIcon(QIcon(":/images/ImageResources/play.png"));
    }
    else{
        pCountDownTimer->start(1000);
        isTimerPlaying = true;
        pPlayButton->setIcon(QIcon(":/images/ImageResources/pause.png"));
    }
}

void WindowWidget::onResetButtonClicked()
{
    if(isTimerPlaying == true)
    {
        if(pCountDownTimer->isActive()){
            pCountDownTimer->stop();
        }
    }

    pPlayButton->setIcon(QIcon(":/images/ImageResources/play.png"));
    isTimerPlaying = false;

    rulerSettedSeconds =0;
    pTimeLabel->setText("00:00");
    pDialClock->setupClockTime(0.0);
    pStrechRuler->setRulerReset();
}
```

定时器的处理函数中，每次timeout后，我们需要将当前的时间放进timelabel中，所以我们使用一个成员变量rulerSettedSeconds去记录当前时间的秒数；同时timeout后streachruler中的尺子长度我们也需要改变；尺子长度改变我们封装成updateRulerHeight函数，window widget中将比率传过去；这里是父控件通知子控件，不需要信号槽，只需要将子控件的对应处理函数声明为public就行；

```cpp
class WindowWidget : public QWidget
{
private:
    int rulerSettedSeconds;
}

void WindowWidget::onCountDownTimerProcess()
{
    if(rulerSettedSeconds <=0)
    {
        onResetButtonClicked();
    }

    QString formateTime = calculateFormatTimeWithSeconds(rulerSettedSeconds);
    pTimeLabel->setText(formateTime);

    float currentRatio = rulerSettedSeconds / 3600.0;
    pStrechRuler->updateRulerHeight(currentRatio);
    rulerSettedSeconds--;
}

// strechruler
class StrechRuler : public QWidget
{
public:
    void updateRulerHeight(float value);
    void setRulerReset();
}

void StrechRuler::updateRulerHeight(float value)
{
    int currentY = value * RULER_MAX_HEGIHT;
    pRulerHeader->move(originalPostion.rx(), currentY);

    repaint();
}

void StrechRuler::setRulerReset()
{
    pRulerHeader->move(originalPostion.rx(),0);
    currentPosY =0;
    repaint();
}
```



(9) 考虑到我们在定时器启动的情况下，有可能去拉动尺子，所以尺子拉动停下的时候，我们不仅仅希望它显示新的时间，还希望显示新的时间后，定时器关闭，start按钮显示play图片；

```cpp
void WindowWidget::onRulerStrechDone(float value)
{
    ...
    if (pCountDownTimer->isActive()) {
        pCountDownTimer->stop();
        isTimerPlaying = false;
    }
    pPlayButton->setIcon(QIcon(":/images/ImageResources/play.png"));
}
```



## 总结

其实这个自定义控件的案例还是比较复杂的，大量的运用了控件之间的通信；ui部分个人感觉难度不大，这里主要总结一下控件之间通信



控件之间的通信，可以总结为以下三种，我们能看出不是所有的控件之间的通信我们都要使用信号槽

1.  子控件传递信息给父控件，子控件通知父控件，使用信号槽；

    具体来说，此时槽函数定义在接收信号的父控件中，修饰为private，然后父控件中connect信号和槽
2.  互相独立的子控件之间通信，使用信号槽；

    具体来说，我们先找到这两个子控件的公共父控件，然后槽函数定义在接收信号的子控件中，修饰为public，然后父控件中connect信号和槽（如果子空间的槽函数不修饰为public，无法在父控件中访问到，自然也就不能connect了）
3.  父控件传递信息给子控件，父控件通知子控件，此时不需要使用信号槽

    具体来说，子控件的处理函数直接修饰为public，然后我们就可以直接在父控件中调用子控件的public函数了
4. QT中一些控件自定义了一些信号，此时直接在控件所在的父控件中实现槽函数；



rulerheader鼠标上下拖动：

本质就是rulerheader要在它的父控件里拖动，所以rulerheader需要传递鼠标偏移量给父控件；同时鼠标停下时，rulerheader需要通知父控件记录rulerheader的位置；使用信号槽，rulerheader中的mouseEvent里发出信号，然后strechruler里实现槽函数，strechruler中connect



rulerheader上下拖动时，表盘控件中的指针也转动：

本质就是rulerheader拖动时，传递strechruler的尺子比率给表盘控件；rulerheader拖动时strechruler的所有操作已经封装在了对应的槽函数中；所以在strechruler对应的槽函数中，我们传递尺子比率给表盘控件；使用信号槽，strechruler的对应rulerheader move时的槽函数中发出信号，然后表盘控件中实现槽函数，window widget中connect



rulerheader上下拖动时，timelabel设置对应时间

本质就是rulerheader拖动时，传递strechruler的尺子比率给timelabl（自带控件的父控件）；所以在strechruler对应的槽函数中，我们传递尺子比率给window widget；使用信号槽，strechruler中对应的槽函数中发出信号，然后timelabel的父控件里实现对应的槽函数，window widget中connect



start按钮选中定时器开启，取消选中定时器关闭，reset按钮点击后所有控件复位

按钮选中，取消选中，点击都是QT自带的信号，所以直接在按钮所在window widget里实现对应的槽函数即可；



window widget里的各种函数中，可能需要去传递信息给子控件或者通知子控件

此时直接在子控件中去实现函数，注意一定要修饰为public；然后window widget里直接调用这些public函数操作子控件

