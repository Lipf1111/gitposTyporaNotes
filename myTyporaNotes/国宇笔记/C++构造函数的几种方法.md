### 构造函数

####  无参构造函数：构造函数内无形参

```c++
//.h文件中,函数声明;
class color{
private:
    int m_red;
    int m_green;
    int m_blue;
public:
    Color();
	~Color();
};
```

```c++
//.cpp文件中，函数实现
Color::Color(){
    m_red = 0;//初始化成员变量
    m_green = 0;
    m_blue = 0;
}
```

```c++
//.cpp文件中对象实例化
Color clr;
```

#### 有参构造函数：r、g、b 是构造函数的三个形参，

在函数体内初始化成员变量

```c++
//.h文件中,函数声明
class color{
    Color(int r, int g, int b);
	~Color();
};
```

```c++
//.cpp文件中，函数实现
Color::Color(int r, int g, int b) {
    m_red = r;
    m_green = g;
    m_blue = b;
}
```

```c++
//.cpp文件中对象实例化
//在栈上创建3个Color对象，
    Color Red(255, 0, 0);
    Color Green(0, 255, 0);
    Color Blue(0, 0, 255);
```

#### 使用初始化列表来初始化字段

```c++
//通过初始化列表来初始化成员变量，在效率上和在构造函数体内初始化变量是一样的，没有优势，仅仅是在书写上方便，尤其是成员变量较多时，这种写法非常简单明了。
Color::Color(int r, int g, int b) : m_red(r), m_green(g), m_blue(b) {
    ...
}
//将类中的变量m_red...等分别赋值r...
```

等价于：

```c++
Color::Color(int r, int g, int b) {
	m_red=r;
	m_green=g;
	m_blue=b;
    ...
}
```

或者

```c++
Color::Color(int m_red, int m_green, int m_blue) {
	this.m_red=m_red;
	this.m_green=m_green;
	this.m_blue=m_blue;
    ...
}
```

```c++
//另一种初始化方式
class Circle{
public:
    Point center;
    int r;
public:
    Circle(int x,int y, int r):center(x,y){
        this->r=r;
    }
}
```

```c++
#include "bits_stdc++.h"
using namespace std;
class TEST {
private:
	char m_cGender;
	int  m_iAge;
	char m_Name[5];
	string m_s;
public:
	TEST() {
		//test('b', 0,"AAA");
		this->m_cGender = 'g';
		this->m_iAge = 0;
		strcpy(this->m_Name, "AAA");
		this->m_s ="this is constructor without parameters";
	}
	TEST(int m_iAge) {
		this->m_cGender = 'g';
		this->m_iAge = m_iAge;
		strcpy(this->m_Name, "BBB");
		this->m_s = "this is constructor with 1 parameters";
	}
	TEST(char m_cGender,int m_iAge) {
		this->m_cGender = m_cGender;
		this->m_iAge = m_iAge;
		strcpy(this->m_Name, "CCC");
		this->m_s = "this is constructor with 2 parameters";
	}
	TEST(char m_cGender, int m_iAge,string m_Name) {
		this->m_cGender = m_cGender;
		this->m_iAge = m_iAge;
		strcpy(this->m_Name, m_Name.c_str());
		this->m_s = "this is constructor with 3 parameters" ;
	}
	~TEST() {
		this->m_s = "this is disconstructor" ;
	}
	void putStr() {
		cout << this->m_Name<<endl<< this->m_cGender<< endl \
			<< this->m_iAge<< endl <<this->m_s << endl;
	}
};
int main(){
	TEST test;
	test.putStr();
    system("pause");
    return 0;
}
```

```out
AAA
g
0
this is constructor without parameters
请按任意键继续. . .
```

```out
//此情况实例化TEST test(33);
BBB
g
33
this is constructor with 1 parameters
请按任意键继续. . .
```

```out
//此情况实例化TEST test('b',33);
CCC
b
33
this is constructor with 2 parameters
请按任意键继续. . .
```

```c++
//此情况实例化TEST test('b',33,"DDD");
DDD
b
33
this is constructor with 3 parameters
请按任意键继续. . .
```

若构造函数为

```c++
TEST(char m_cGender, int m_iAge,char* m_Name)
```

实例化仍为上述测试最后一个，会报错

C2664	“TEST::TEST(const TEST &)”: 无法将参数 3 从“const char [4]”转换为“char *

```c++
//可以改成下式
TEST(char m_cGender, int m_iAge,const char* m_Name)
```

#### 另外一种情况

```c++
TEST test();
test.putStr();
```

将会报错

C2228	“.putStr”的左边必须有类/结构/联合

相当于直接调用构造函数，但此时还未分配空间

可以换成：

```c++
TEST* pClass = new TEST;
pClass->putStr();
...
delete pClass;
```
或者：

```c++
TEST test;
test.putStr();
```

即便类中加上

```c++
void operator()() {
	cout << this->m_Name << endl << this->m_cGender << endl \
		<< this->m_iAge << endl << this->m_s << endl;
}
```
也只能调用为：

```c++
TEST test;
test();
/*
AAA
g
0
this is constructor without parameters
请按任意键继续. . .
*/
//不能直接TEST test();
```
#### 拷贝构造函数

构造函数添加

```c++
TEST(const TEST & tmp) {
	this->m_cGender = tmp.m_cGender;
	this->m_iAge = tmp.m_iAge;
	strcpy(this->m_Name, tmp.m_Name);
	this->m_s = "this is constructor in light copy way";
}
```
```c++
//main函数里
TEST test(33);
TEST tmp(test);
tmp.putStr();
```
```out
//结果
BBB
g
33
this is constructor in light copy way
请按任意键继续. . .
```

但有一种情况会出错：

```c++
class Node{
public:
    int* number;//指针是问题关键
public:
    Node（const Node& t){//默认的拷贝构造函数就这么干的
        this->number=t.number;
	}
};
```

 看看它干了啥！！！他把 t 的number里存的地址赋给了新对象的number，这意味着什么？ 意味着新对象的`number`和`t.number`指着同一个空间，我在新对象里改了number所指空间的值，那么`t.number`指的空间值也就变了，这一般不是我们想要的，我们一般想要两个对象互不干扰，所以系统默认给的那种只是简单拷贝了指针地址的这种行为，**我们叫浅拷贝，解决方法叫深拷贝**，如下： 

```c++
...
Node(const Node& t){
    number=new int();
    *number=*(t.number);
}
...
```

