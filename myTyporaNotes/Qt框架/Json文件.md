```c++
#include <QWidget>
#include <QJsonObject>
#include <QJsonDocument>
#include <QCryptographicHash>
#include <QDebug>
Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);
    //给密码进行Md5加密
    QByteArray pwd = QCryptographicHash::hash("lipengfei",QCryptographicHash::Md5);
    //构造Json文件
    QJsonObject obj;
    obj.insert("姓名",QString("李鹏飞"));
    obj.insert("年龄",25);
    obj.insert("账号",QString("李鹏飞"));
    obj.insert("密码",QString(pwd.toHex()));
    /*一个QJsonDocument包含一个QJsonObject对象或者一个QJsonObject数组*/
    QJsonDocument doc(obj);
    QByteArray array = doc.toJson();
    qDebug() << array.toStdString().data();

    //解析Json文件
    QJsonObject obj2 = QJsonDocument::fromJson(array).object();
    if(obj2.value("姓名").toString() == "李鹏飞"){
        qDebug() << "yes";
    }
    qDebug() << obj2.length();//获取Json文件长度
    qDebug() << obj2.value("姓名").toString();//通过key值获得value

}
```

