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



(4) strechruler随着rulerheader移动，绘制对应长度的尺子
