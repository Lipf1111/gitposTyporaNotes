####  pointer四个智能指针shared_ptr,unique_ptr,weak_ptr,auto_ptr

头文件在 <memory>  中

C++里面的四个智能指针: auto_ptr, shared_ptr, weak_ptr, unique_ptr 其中后三个是c++11支持，并且第一个已经被11弃用。

**为什么要使用智能指针：**

智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针就是一个类，当超出了类的作用域是，类会自动调用析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。

1. ##### auto_ptr（c++98的方案，cpp11已经抛弃）

采用所有权模式。

```c++
auto_ptr< string> p1 (new string ("I reigned lonely as a cloud.”));
auto_ptr<string> p2;
p2 = p1; //auto_ptr不会报错.
```

此时不会报错，p2剥夺了p1的所有权，但是当程序运行时访问p1将会报错。所以auto_ptr的缺点是：存在潜在的内存崩溃问题！

2. ##### unique_ptr（替换auto_ptr）

unique_ptr实现独占式拥有或严格拥有概念，保证同一时间内只有一个智能指针可以指向该对象。它对于避免资源泄露(例如“以new创建对象后因为发生异常而忘记调用delete”)特别有用。

采用所有权模式，还是上面那个例子

```c++
unique_ptr p3 (new string ("auto"));  //#4
unique_ptr p4;            //#5
p4 = p3;//此时会报错！！
```

编译器认为p4=p3非法，避免了p3不再指向有效数据的问题。因此，unique_ptr比auto_ptr更安全。

另外unique_ptr还有更聪明的地方：当程序试图将一个 unique_ptr 赋值给另一个时，如果源 unique_ptr 是个临时右值，编译器允许这么做；如果源 unique_ptr 将存在一段时间，编译器将禁止这么做，比如：

```c++
unique_ptr<string> pu1(new string ("hello world"));
unique_ptr<string> pu2;
pu2 = pu1;                                      // #1 not allowed
unique_ptr<string> pu3;
pu3 = unique_ptr<string>(new string ("You"));   // #2 allowed
```

其中#1留下悬挂的unique_ptr(pu1)，这可能导致危害。而#2不会留下悬挂的unique_ptr，因为它调用 unique_ptr 的构造函数，该构造函数创建的临时对象在其所有权让给 pu3 后就会被销毁。这种随情况而定的行为表明，unique_ptr 优于允许两种赋值的auto_ptr 。

注：如果确实想执行类似与#1的操作，要安全的重用这种指针，可给它赋新值。C++有一个标准库函数std::move()，让你能够将一个unique_ptr赋给另一个。例如：

```c++
unique_ptr<string> ps1, ps2;
ps1 = unique_ptr<string>(new string("hello"));
ps2 = move(ps1);
ps1 = unique_ptr<string>(new string("alexia")); 
cout << *ps2 << *ps1 << endl;
```
```c++
unique_ptr<string> pu1(new string("hello world"));
unique_ptr<string> pu2;
pu2 = move(pu1);
//pu1=unique_ptr<string>(new string("You"));//输出pu1地址与You
cout << pu1 << endl;//00000000
cout << *pu1 << endl;//无反应
//cout << *pu2 << endl;//hello world
```

3. ##### shared_ptr

shared_ptr实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。从名字share就可以看出了资源可以被多个指针共享，它使用**计数机制**来表明资源被几个指针共享。可以通过成员函数use_count()来查看资源的所有者个数。除了可以通过new来构造，还可以通过传入auto_ptr, unique_ptr,weak_ptr来构造。当我们调用release()时，当前指针会释放资源所有权，计数减一。当计数等于0时，资源会被释放。

shared_ptr 是为了解决 auto_ptr 在对象所有权上的局限性(auto_ptr 是独占的), 在使用引用计数的机制上提供了可以共享所有权的智能指针。

**成员函数：**

use_count 返回引用计数的个数

unique 返回是否是独占所有权( use_count 为 1)

swap 交换两个 shared_ptr 对象(即交换所拥有的对象)

reset 放弃内部对象的所有权或拥有对象的变更, 会引起原有对象的引用计数的减少

get 返回内部对象(指针), 由于已经重载了()方法, 因此和直接使用对象是一样的.如 `shared_ptr<int> sp(new int(1));` sp 与 sp.get()是等价的
		只要将 new 运算符返回的指针 p 交给一个 shared_ptr 对象“托管”，就不必担心在哪里写`delete p`语句——实际上根本不需要编写这条语句，托管 p 的 shared_ptr 对象在消亡时会自动执行`delete p`。而且，该 shared_ptr 对象能像指针 p —样使用，即假设托管 p 的 shared_ptr 对象叫作 ptr，那么 *ptr 就是 p 指向的对象。

通过 shared_ptr 的构造函数，可以让 shared_ptr 对象托管一个 new 运算符返回的指针，写法如下： 

```c++
 shared_ptr<T> ptr(new T); // T 可以是 int、char、类等各种类型 
```

此后，ptr 就可以像 T* 类型的指针一样使用，即 *ptr 就是用 new 动态分配的那个对象。

多个 shared_ptr 对象可以共同托管一个指针 p，当所有曾经托管 p 的 shared_ptr 对象都解除了对其的托管时，就会执行`delete p`。

例如下面的程序： 

```c++
#include <iostream>
#include <memory>
using namespace std;
class A{
public:
    int i;
    A(int n):i(n) { };
    ~A() { cout << i << " " << "destructed" << endl; }
};
int main(){
    shared_ptr<A> sp1(new A(2)); //A(2)由sp1托管，
    shared_ptr<A> sp2(sp1);       //A(2)同时交由sp2托管
    shared_ptr<A> sp3;
    sp3 = sp2;   //A(2)同时交由sp3托管
    cout << sp1->i << "," << sp2->i <<"," << sp3->i << endl;
    A * p = sp3.get();      // get返回托管的指针，p 指向 A(2)
    cout << p->i << endl;  //输出 2
    sp1.reset(new A(3));    // reset导致托管新的指针, 此时sp1托管A(3)
    sp2.reset(new A(4));    // sp2托管A(4)
    cout << sp1->i << endl; //输出 3
    sp3.reset(new A(5));    // sp3托管A(5),A(2)无人托管，被delete
    cout << "end" << endl;
    return 0;
}
```
```out
程序的输出结果如下：
2,2,2
2
3
2 destructed
end
5 destructed
4 destructed
3 destructed
```

> 注：只有指向动态分配的对象的指针才能交给 shared_ptr 对象托管。将指向普通局部变量、全局变量的指针交给 shared_ptr 托管，编译时不会有问题，但程序运行时会出错，因为不能析构一个并没有指向动态分配的内存空间的指针。 

注意，不能用下面的方式使得两个 shared_ptr 对象托管同一个指针：

```c++
A* p = new A(10);
shared_ptr <A> sp1(p), sp2(p);
```

sp1 和 sp2 并不会共享同一个对 p 的托管计数，而是各自将对 p 的托管计数都记为 1（sp2 无法知道 p 已经被 sp1 托管过）。这样，当 sp1 消亡时要析构 p，sp2 消亡时要再次析构 p，这会导致程序崩溃。




4. ##### weak_ptr

weak_ptr 是一种不控制对象生命周期的智能指针, 它指向一个 shared_ptr 管理的对象. 进行该对象的内存管理的是那个强引用的 shared_ptr. weak_ptr只是提供了对管理对象的一个访问手段。weak_ptr 设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作, 它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, 它的**构造和析构不会引起引用记数的增加或减少**。weak_ptr是用来解决shared_ptr相互引用时的**死锁**问题,如果说两个shared_ptr相互引用,那么这两个指针的引用计数永远不可能下降为0,资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和shared_ptr之间可以相互转化，shared_ptr可以直接赋值给它，它可以通过调用lock函数来获得shared_ptr。

```c++
class B;
class A{
public:
    shared_ptr<B> pb_;
    ~A(){
        cout<<"A delete\n";
    }
};
class B{
public:
    shared_ptr<A> pa_;
    ~B(){
        cout<<"B delete\n";
    }
};
void fun(){
    shared_ptr<B> pb(new B());
    shared_ptr<A> pa(new A());
    pb->pa_ = pa;
    pa->pb_ = pb;
    cout<<pb.use_count()<<endl;
    cout<<pa.use_count()<<endl;
}
int main(){
    fun();
    return 0;
}
```

可以看到fun函数中pa ，pb之间互相引用，两个资源的引用计数为2，当要跳出函数时，智能指针pa，pb析构时两个资源引用计数会减一，但是两者引用计数还是为1，导致跳出函数时资源没有被释放（A B的析构函数没有被调用），如果把其中一个改为weak_ptr就可以了，我们把类A里面的`shared_ptr pb_; `改为`weak_ptr pb_; `运行结果如下，这样的话，资源B的引用开始就只有1，当pb析构时，B的计数变为0，B得到释放，B释放的同时也会使A的计数减一，同时pa析构时使A的计数减一，那么A的计数为0，A得到释放。

注意的是我们不能通过weak_ptr直接访问对象的方法，比如B对象中有一个方法print(),我们不能这样访问，`pa->pb_->print(); `因为pb\_是一个weak_ptr，应该先把它转化为shared_ptr,如：

```c++
shared_ptr p = pa->pb_.lock();
p->print();
```

**当两个对象相互使用一个shared_ptr成员变量指向对方，会造成循环引用，使引用计数失效，从而导致内存泄漏。** 

