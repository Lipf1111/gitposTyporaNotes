```c++
MyMainWindow::MyMainWindow(QWidget *parent)
    : QMainWindow(parent)
{
    this->setWindowTitle("myMainWindow");
    this->resize(800,600);

    /*添加菜单栏*/
    //1.定义菜单栏
    QMenuBar *menuBar = new QMenuBar(this);
    //2.将菜单栏添加到窗口上
    this->setMenuBar(menuBar);

    /*添加菜单*/
    //1.定义菜单
    QMenu *file = new QMenu("文件",this);
    QMenu *edit = new QMenu("编辑",this);
    //2.将菜单添加到菜单项中
    menuBar->addMenu(file);
    menuBar->addMenu(edit);

    /*添加菜单项*/
    //1.定义菜单项
    QAction *open = new QAction("打开",this);
    QAction *_new = new QAction("新建",this);
    //2.将菜单项添加到file菜单中
    file->addAction(open);
    file->addSeparator();//添加分割线
    file->addAction(_new);

    /*给菜单项添加快捷键*/
    open->setShortcut(QKeySequence(Qt::CTRL+Qt::SHIFT+Qt::Key_O));
    _new->setShortcut(QKeySequence(tr("Ctrl+p")));
    /*当点击菜单项的触发动作*/
    connect(_new,&QAction::triggered,[](){qDebug() << "_new clicked";});
    connect(open,&QAction::triggered,[](){qDebug() << "open clicked";});

    /*添加工具栏*/
    //1.定义工具栏
    QToolBar *tool = new QToolBar(this);
    //2.将工具栏添加到窗口上
    this->addToolBar(tool);
    //将open、_new菜单项添加到工具栏中
    tool->addAction(open);
    tool->addSeparator();//设置分割线
    tool->addAction(_new);
    //此时工具栏支持上下左右停靠，以及中间浮动，设置这些属性
    tool->setFloatable(false);//设置不运行浮动
    tool->setAllowedAreas(Qt::LeftToolBarArea|Qt::RightToolBarArea);//设置仅允许左右停靠
    tool->setMovable(false);//设置不允许拖动

    /*添加状态栏*/
    //1.定义状态栏
    QStatusBar *statusBar = new QStatusBar(this);
    //2.将状态栏添加到窗口上
    this->setStatusBar(statusBar);
    QLabel *label1 = new QLabel("添加状态栏左侧提示信息");
    QLabel *label2 = new QLabel("添加状态栏右侧提示信息");
    QLabel *label3 = new QLabel("插入窗口提示信息");
    statusBar->addWidget(label1);
    statusBar->addPermanentWidget(label2);
    statusBar->insertWidget(1,label3);//在中间1的位置插入一个窗口小部件

    /*添加铆接部件*/
    //1.创建铆接部件
    QDockWidget *dock = new QDockWidget("构造函数中指定铆接部件名字",this);
    dock->setWindowTitle("hahaha");//设置铆接部件名字，更改了上一行的名字，显示为hahaha
    //2.将铆接部件添加到主窗口上，需要直接指定铆接部件的位置
    this->addDockWidget(Qt::TopDockWidgetArea,dock);
    dock->setAllowedAreas(Qt::TopDockWidgetArea|Qt::BottomDockWidgetArea);//允许铆接部件位置为上和下
    dock->setFloating(false);//设置不允许在主窗口内浮动，但是超出主窗口后实际还可以浮动

    /*在主窗口中添加一个QTextEdit部件*/
    QTextEdit *text = new QTextEdit("床前明月光\n疑似地上霜\n举头望明月",this);
    this->setCentralWidget(text);
        
	//给菜单file、菜单项open添加图标
    open->setIcon(QIcon(QPixmap("../image/1.jpg")));
    file->setIcon(QIcon(QPixmap("../image/2.jpg")));

}
```

