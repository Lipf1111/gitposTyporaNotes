强制类型转换

​	C中是直接在变量或者表达式前面加上（小括号括起来的）目标类型来进行转换，一招走天下，操作简单，但是由于太过直接，缺少检查，因此容易发生编译检查不到错误，而人工检查又及其难以发现的情况；而C++中引入了下面四种转换：

-  #### static_cast
  
  - 用于基本类型间的转换
  
  - 不能用于基本类型指针间的转换
  
  - 用于有继承关系类对象间的转换和类指针间的转换
  

```c++
char a = 'a';
int b = static_cast<char>(a);//正确，将char型数据转换成int型数据

double *c = new double;
void *d = static_cast<void*>(c);//正确，将double指针转换成void指针

int e = 10;
const int f = static_cast<const int>(e);//正确，将int型数据转换成const int型数据

const int g = 20;
int *h = static_cast<int*>(&g);//编译错误，static_cast不能转换掉g的const属性
```

```c++
class Base
{};

class Derived : public Base
{}

Base* pB = new Base();
if(Derived* pD = static_cast<Derived*>(pB))
{}//下行转换是不安全的(坚决抵制这种方法)

Derived* pD = new Derived();
if(Base* pB = static_cast<Base*>(pD))
{}//上行转换是安全的
```
- #### dynamic_cast
  
  - 用于有继承关系的类指针间的转换
  
  - 用于有交叉关系的类指针间的转换
  
  - 具有类型检查的功能
  
  - 需要虚函数的支持
  
  - 转换方式
  
    - dynamic_cast< type\* >(e)
      　type必须是一个类类型且必须是一个有效的指针
  
    - dynamic_cast< type& >(e)
      type必须是一个类类型且必须是一个左值
  
    - dynamic_cast< type&& >(e)
      **type必须是一个类类型且必须是一个右值 
  
  - e的类型必须符合以下三个条件中的任何一个：
      - e的类型是目标类型type的公有派生类
      - e的类型是目标type的共有基类
      - e的类型就是目标type的类型。 
  
  在类层次间进行**上行转换**时，dynamic_cast和static_cast的效果是一样的；在进行**下行转换**时，dynamic_cast具有**类型检查的功能**，比static_cast更安全。dynamic_cast是唯一无法由旧式语法执行的动作，也是唯一**可能耗费重大运行成本**的转型动作。 
  
  （1）指针类型
  举例，Base为包含至少一个虚函数的基类，Derived是Base的共有派生类，如果有一个指向Base的指针bp，我们可以在运行时将它转换成指向Derived的指针，代码如下：
  
  ```c++
  if(Derived *dp=dynamic_cast<Derived *>(bp)){//若此基类指针是由子类指针上行转换形成则正确？
    //使用dp指向的Derived对象  
  }else{
    //使用bp指向的Base对象  
  }
  ```
  
  值得注意的是，在上述代码中，if语句中定义了dp，这样做的好处是可以在一个操作中同时完成类型转换和条件检查两项任务。 
  
  （2）引用类型
  
  因为不存在所谓空引用，所以引用类型的dynamic_cast转换与指针类型不同，在引用转换失败时，会抛出std::bad_cast异常，该异常定义在头文件typeinfo中。
  
  ```c++
  void f(const Base &b){
      try{
      	const Derived &d = dynamic_cast<const Base &>(b);  
          //使用b引用的Derived对象
      }catch(std::bad_cast){
          //处理类型转换失败的情况
      }
  }
  ```
  
- #### reinterpret_cast

  - 用于指针间的类型转换
  -  用于整数和指针间的类型转换

  reinterpret_cast<new_type> (expression)

  reinterpret_cast运算符是用来处理无关类型之间的转换；它会产生一个新的值，这个值会有与原始参数（expression）有完全相同的比特位。

  ```c++
  unsigned short Hash( void *p ) {
      unsigned int val = reinterpret_cast<unsigned int>( p );
      return ( unsigned short )( val ^ (val >> 16));
  }
  ```

- #### const_cast

  - 用于去掉变量的const属性
  - 转换的目标类型必须是指针或者引用

  ```c++
  const int consatant = 20;
  Printer(const_cast<int *>(&consatant));
  ```

  ```c++
  char * p=const_cast<char *>("12345");
  ```

​	​	在C++中，普通类型可以通过类型转换构造函数转换为类类型，那么类可以转换为普通类型吗？答案是肯定的 。但是在工程应用中一般不用类型转换函数，因为无法抑制隐式的调用类型转换函数（类型转换构造函数可以通过explicit来抑制其被隐式的调用），而隐式调用经常是bug的来源。实际工程中替代的方式是定义一个普通函数，通过显式的调用来达到类型转换的目的。

```c++
class test{
    int m_value;
    //...
public:
    int operator int(){  //类型转换函数 
         return m_value;
    }
    int toInt(){ //显示调用普通函数来实现类型转换
    	return m_value
    }
};
int main(){
    //...
    test a(5);
    int i = a;
    //...
    return 0;
}
```

C++中四种类型转换是：static_cast, dynamic_cast, const_cast, reinterpret_cast

**1、const_cast**

用于将const变量转为非const

**2、static_cast**

用于各种隐式转换，比如非const转const，void*转指针等, static_cast能用于多态向上转化，如果向下转能成功但是不安全，结果未知；

**3、dynamic_cast**

用于动态类型转换。只能用于**含有虚函数的类**，用于类层次间的向上和向下转化。只能转指针或引用。向下转化时，如果是非法的对于指针返回NULL，对于引用抛异常。要深入了解内部转换的原理。

向上转换：指的是子类向基类的转换

向下转换：指的是基类向子类的转换

它通过判断在执行到该语句的时候变量的运行时类型和要转换的类型是否相同来判断是否能够进行向下转换。

4、reinterpret_cast

几乎什么都可以转，比如将int转指针，可能会出问题，尽量少用；

5、为什么不使用C的强制转换？

C的强制转换表面上看起来功能强大什么都能转，但是转化不够明确，不能进行错误检查，容易出错。