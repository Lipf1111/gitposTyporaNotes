# 消息对话框

```c++
MyMainWindow::MyMainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MyMainWindow)
{
    QMessageBox::StandardButton ret ;
    //int ret;也可以
    //第二个参数可以更改选项，第三个参数设置默认选项
    ret = QMessageBox::information(this,"提示","你确定？",
                                   QMessageBox::Ok|QMessageBox::YesAll,QMessageBox::YesAll);
    if(ret == QMessageBox::Ok){
        qDebug() << "选择了Ok";
    }else if(ret == QMessageBox::YesAll){
        qDebug()<< "选择了YesAll";
    }
}
```

# 字体对话框

```c++
    bool ok;
	//第一个参数传入个地址，ok变量如果为true说明获得了字体
	//第二个参数是打开字体对话框时默认选择的字体
    QFont font = QFontDialog::getFont(&ok,QFont("宋体"),this);
    if(ok == true){//说明获取到了字体
        qDebug() << "字体为" << font.family() << "，字体大小为：" <<font.pointSize();
    }
```

# 颜色对话框

```c++
    QColor color;
    color = QColorDialog::getColor();
    //获得rgb三个参数
    qDebug() << "r = " << color.red() << "g = " << color.green() << " b = " << color.blue();
```

# 文件对话框

```c++
    //第一个参数指定父对象，第二个参数是打开的对话框名字，第三个参数默认打开的路径，第四个参数为筛选出需要的文件类型
	QString fileName = QFileDialog::getOpenFileName(this,"打开文件对话框的名字","/home","*.c *.png");
//获得打开的文件路径
    qDebug() << fileName;
```

