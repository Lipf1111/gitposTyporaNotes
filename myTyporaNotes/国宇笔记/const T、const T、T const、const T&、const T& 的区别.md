### const T、const T*、T *const、const T&、const T\*& 的区别

传送门: http://blog.csdn.net/luoweifu/article/details/45600415

这里的T指的是一种数据类型，可以是int、long、doule等基本数据类型，也可以是自己类型的类型class。单独的一个const你肯定知道指的是一个常量，但const与其他类型联合起来的众多变化。

#### const T

定义一个常量，声明的同时必须进行初始化。一旦声明，这个值将不能被改变。

```c++
int i = 5;
const int constInt = 10; //正确
const int constInt2 = i; //正确
constInt = 20; //错误，常量值不可被改变
const int constInt3; //错误，未被初始化
```

#### const T*

指向常量的指针，不能用于改变其所指向的对象的值。

```c++
const int i = 5;
const int i2 = 10;
const int* pInt = &i; //正确，指向一个const int对象，即i的地址
//*pInt = 10; //错误，不能改变其所指缶的对象
pInt = &i2; //正确，可以改变pInt指针本身的值,此时pInt指向的是i2的地址
const int* p2 = new int(8); //正确，指向一个new出来的对象的地址
delete p2; //正确
//int* pInt = &i; //错误，i是const int类型，类型不匹配，不能将const int * 初始化为int *
int nValue = 15;
const int * pConstInt = &nValue; //正确，可以把int *赋给const int *，但是pConstInt不能改变其所指向对象的值，即nValue
*pConstInt = 40; //错误，不能改变其所指向对象的值
```

#### const int* 与int* const的区别

指针本身就是一种对象，把指针定义为常量就是常量指针，也就是int* const的类型，也可以写成int *const，声明时必须初始化。

```c++
int nValue = 10;
int* const p = &nValue;
int *const p2 = &nValue;
```

`const int*` 指针指向的对象不可以改变，但指针本身的值可以改变；int* const 指针本身的值不可改变，但其指向的对象可以改变。

```c++
int nValue1 = 10;
int nValue2 = 20;
int* const constPoint = &nValue1;
//constPoint = & nValue2; //错误，不能改变constPoint本身的值
*constPoint = 40; //正确，可以改变constPoint所指向的对象，此时nValue1 = 40
const int nConstValue1 = 5;
const int nConstValue2 = 15;
const int* pPoint = &nConstValue1;
//*pPoint = 55; //错误，不能改变pPoint所指向对象的值
pPoint = &nConstValue2; //正确，可以改变pPoint指针本身的值，此时pPoint绑定的是nConstValue2对象，即pPoint为nConstValue2的地址
```


const int* const 是一个指向常量对象的常量指针，即不可以改变指针本身的值，也不可以改变指针指向的对象。

```c++
const int nConstValue1 = 5;
const int nConstValue2 = 15;
const int* const pPoint = &nConstValue1;
//*pPoint = 55; //错误，不能改变pPoint所指向对象的值
//pPoint = &nConstValue2; //错误，不能改变pPoint本身的值
```

#### const T&

对常量(const)的引用，又称为常量引用，常量引用不能修改其绑定的对象。

```c++
int i = 5;
const int constInt = 10;
const int& rConstInt = constInt; //正确，引用及绑定的值都是常量
rConstInt = 5; //错误，不能改变引用所指向的对象
```

允许为一个常量引用绑定一个非常量对象、字面值，甚至是表达式；引用的类型与引用所指向的类型必须一致。

```c++
int i = 5;
int& rInt = i; //正确，int的引用
const int constInt = 10;
const int& rConstInt = constInt; //正确，引用及绑定的值都是常量
const int& rConstInt2 = rInt; //正确，用rInt绑定的对象进行赋值
rInt = 30; //这时，rConstInt2、rInt、i的值都为30
//rConstInt2 = 30; //错误，rConstInt2是常量引用，rConstInt2本身不能改变所指向的对象
int i2 = 15;
const int& rConstInt3 = i2; //正确，用非常量的对象为其赋值
const int& rConstInt4 = i + i2; //正确，用表达式为其赋值,值为45
i = 20; //此时i=20, rInt = 20, rConstInt4 = 45,说明rConstInt4绑定的是i + i2的临时变量
const int& rConstInt5 = 50; //正解，用一个常量值为其赋值
```


#### const T*&与T *const&

指向常量对象的指针的引用，这可以分两步来理解：1.const T*是指向常量的指针；2.const T*&指向常量的指针的引用。

```c++
const int nConstValue = 1; //常量对象
const int nConstValue2 = 2; //常量对象
const int* pConstValue = &nConstValue; //指向常量对象的指针
const int* pConstValue2 = &nConstValue2; //指向常量对象的指针
const int*& rpConstValue = pConstValue; //指向常量对象的指针的引用
//*rpConstValue = 10; //错误，rpConstValue指向的是常量对象，常量对象的值不可改变
rpConstValue = pConstValue2; //正确，此时pConstValue的值等于pConstValue2
//指向常量对象的指针本身是对象，引用可以改变绑定对象的值
int nValue = 5;
int nValue2 = 10;
int *const constPoint = &nValue; //常量指针
int *const constPoint2 = &nValue2; //常量指针
int *const &rpConstPoint = constPoint; //对常量指针的引用,绑定constPoint
//rpConstPoint = constPoint2; //错误，constPoint是常量指针，指针本身的值不可改变
*rpConstPoint = 20; //正确，指针指向的对象可以改变
```

#### 在函数中的应用

我们直接从需求出来，假设有这样一个数据结构：

```c++
typedef struct __Data{
public:
	int value;
	__Data():value(0){}
}Data;
```



##### 1.希望传入一个对象，又不想让函数体修改这个对象。

###### 方式<1>

```c++
void Dealwith(const Data& data){
    cout << data.value << endl;
    //data.value = 5; //错误，data是常量引用，不能改变其绑定的对象
}
```

这种方式还有一个好处是只有在调用函数的时候会绑定对象，传递的是对象的引用，而不是对象，减少函数调用时对象赋值的花销。

###### 方式<2>

```c++
void Dealwith(const Data* pData){
    cout << pData->value << endl;
    //pData->value = 5; //错误，pData是指向常量对象的指针，不能改变其指向的对象
}
```

这种方式与void Dealwith(const Data& data)的功能相同

###### 方式<3>

```c++
Data g_data(20);
void Dealwith(const Data*& pData){
    cout << pData->value << endl;
    //pData->value = 5; //错误，pData绑定的是指向常量对象的指针，常量对象的指针不能改变其指向的对象
    pData = &g_data; //正确，pData是指向常量对象的指针的引用，引用可改变其绑定的对象
}
```

调用如下：

```c++
Data d(10);
const Data* pData = &d;
Dealwith(pData);
cout << pData->value << endl; //此时pData->value为20，d.value还是10
```

这种方式函数未改变传入的对象的值，但可以返回另外一个对象的指针。注意返回的指针必须指向全局的对象，如果返回函数内定义的对象，退出函数作用域后，其指针将无效，这是非常危险的；如果Dealwith是成员函数，也可以返回指向成员的指针。

##### 2.在类中的使用，返回一个类的成员，但不希望调用方修改这个成员。

###### 方式<1>

```c++
class MyData{
public :
    MyData(std::string name, Data data){
        m_name = name;
        m_data = data;
    }
    const Data* GetData(){
        return &m_data;
    }
private:
    std::string m_name;
    Data m_data;
};
```

调用如下：

```c++
MyData mydata("", Data(100));
const Data* pData = mydata.GetData();
cout << pData->value << endl; //pData->value = 100
//pData->value = 50; //错误，pData是指向常量对象的指向，不能改变其指向对象的值
```

###### 方式<2>

有人可能会问GetData也可以写成这样：

```c++
const Data& GetData(){
	return m_data;
}
```

这样的话，调用方常常容易写成这样：

```c++
MyData mydata("", Data(100));
Data data = mydata.GetData(); //这会有个赋值的过程，会把mydata.m_data赋给data
cout << data.value << endl; //data.value = 100
data.value = 50; //正确，data.value=50，但mydata.m_data.value还是100
```

这样调用时会有一个结果赋值的过程，如果Data是一个复杂的类，会有较大的开销，其效果与下面这种方式是一样的：

```c++
Data GetData(){
	return m_data;
}
```

当然，如果调用方这样使用是正确的：

```c++
const Data& GetData(){
	return m_data;
}
MyData mydata("", Data(100));
const Data& data = mydata.GetData(); //这会有个赋值的过程，会把mydata.m_data赋给data
cout << data.value << endl; //data.value = 100
//data.value = 50; //错误，data是一个对常量的引用，不能改变其绑定的对象
```

这对调用方的技术能力要求比较高，如果你是设计方，一定要尽量使接口简单易用。

###### 方式<3>

如果你要传入一个Data进行一些处理，处理完后返回类的成员m_data，可如下实现：

```c++
void DoSomething(const Data*& pData){
    if (pData != NULL){
        // doto: 这里实现你要处理的功能
    }
    pData = &m_data;
}
```

