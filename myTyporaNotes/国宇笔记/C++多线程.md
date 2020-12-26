### **C++多线程**

多线程是多任务处理的一种特殊形式，多任务处理允许让电脑同时运行两个或两个以上的程序。一般情况下，两种类型的**多任务处理**：**基于进程和基于线程**。 

- 基于进程的多任务处理是程序的并发执行。
- 基于线程的多任务处理是同一程序的片段的并发执行。

​		多线程程序包含可以同时运行的两个或多个部分。这样的程序中的每个部分称为一个线程，每个线程定义了一个单独的执行路径。 

####  多线程的缺点

1.　如果有大量的线程,会影响性能,因为操作系统需要在它们之间切换.
2.　更多的线程需要更多的内存空间
3.　线程中止需要考虑对程序运行的影响.
4.　通常块模型数据是在多个线程间共享的,需要防止线程死锁情况的发生 

#### 引入目的

​		使用多线程最主要的目的就是缩短应用程序完成任务的时间。

​		在【为什么需要多线程】总结中有这样一句话：<b>若多个线程都是CPU密集型，那么并不能获取性能上的提升。</b>同理，若都是IO密集型的，也不能提升性能。即这种情况下，使用多线程不能缩短应用程序完成任务的时间。

​		总结：最好在大量CPU计算（控制）和大量IO共存的时候用。

#### 硬件耗时排行

| 硬件英文                         | 硬件中文                | 耗时    |
| -------------------------------- | ----------------------- | ------- |
| L1 cache reference               | 一级缓存引用            | 0.5ns   |
| Branch mispredict                | 分支错误预测            | 5ns     |
| L2 cache reference               | 二级缓存引用            | 7ns     |
| Mutex lock/unlock                | 互斥锁定/解锁           | 100ns   |
| Main memory reference            | 主存引用                | 100ns   |
| Send 1M bytes over 1Gbps network | 通过1Gbps网络发送2K字节 | 10ms    |
| Read 1M sequentially from memory | 从内存中读取1MB         | 0.25ms  |
| Round trip within data center    | 数据中心的往返路程      | 0.5ms   |
| Disk seek                        | 磁盘寻道                | 8~10ms  |
| Read 1MB sequentially from disk  | 从磁盘连续读取1MB       | 20~25ms |
| Compress 1K bytes with Zippy     | 用Zippy压缩1K字节       | 0.01ms  |
| Read 1 MB sequentially from disk | 从磁盘连续读取1MB       | 30ms    |
| Send packet CA->Netherlands->CA  | 在CA向荷兰发包再返回    | 150ms   |

**头文件**#include <thread>  (首字母小写)

#### **创建线程：**

```c++
#include <thread>
#include <iostream>
#include <windows.h>
using namespace std;
// 创建线程
void first(){
	cout << "This is first thread!" << endl;
	std::this_thread::sleep_for(std::chrono::milliseconds(5000));// 睡眠5秒
}
int main(){
	thread t1(first);
	t1.join();// join之后，主线程就会等在这里，等待他执行完毕再执行
	cout << "This is main thread!" << endl;
	system("pause");
    return 0;
}
```

 运行结果分析：first线程定义了之后，立刻就会运行，同时主线程也同时运行。 

 使用**join函数**，**主线程等待子线程执行完毕，才会继续运行**。 

​		这将使该函数返回的时刻与线程中所有操作的完成同步：阻塞调用该函数的线程的执行，直到构造函数上调用的函数返回（如果尚未返回）。

​		调用此函数后，线程对象变得不可连接，并且可以安全地销毁。

#### 线程传递参数

```c++
#include <thread>
#include <iostream>
#include <windows.h>
using namespace std;
void second(int num){
	std::this_thread::sleep_for(std::chrono::milliseconds(1));
	cout << "This is second thread! this thread has a num:" << num << endl;
}
int main(){
	thread t2(second,3);
	t2.join();
	cout << "This is main thread!" << endl;
	system("pause");
    return 0;
}
```

###  detach()函数分析

从调用线程中分离出对象所代表的线程，从而使它们彼此独立执行。

两个线程继续运行，而不会阻塞或以任何方式进行同步。 请注意，**当任何一个执行结束时，其资源将被释放。**

调用此函数后，线程对象变得不可连接，并且可以安全地销毁。

```c++
#include "stdafx.h"
#include <iostream>       // std::cout
#include <thread>         // std::thread, std::this_thread::sleep_for
#include <chrono>         // std::chrono::seconds
void pause_thread(int n){
	std::this_thread::sleep_for(std::chrono::seconds(n));
	std::cout << "pause of " << n << " seconds ended\n";
}
int main(){
	std::cout << "Spawning and detaching 3 threads...\n";
	std::thread(pause_thread, 1).detach();
	std::thread(pause_thread, 2).detach();
	std::thread(pause_thread, 3).detach();
	std::cout << "Done spawning threads.\n";

    std::cout << "(the main thread will now pause for 5 seconds)\n";
    // give the detached threads time to finish (but not guaranteed!):
    pause_thread(5);
    return 0;
}
```

### 传递临时对象做线程参数

#### 陷阱1

​		用detach()时，如果主线程先结束，变量就会被回收；所以用detach()的话，不推荐用引用，同时绝对不能用指针。

#### 陷阱2

​		只要临时对象的 用临时构造TEST类对象作为参数传递给线程，那么就一定能够在主线程结束之前，把线程函数的第二个参数构建出来，从而确保即便detach()子线程也安全运行，程序如下：

```c++
#include <iostream>
#include <thread>
#include <string>
using namespace std;
class TEST{
public:
	int clsAttr;
   //类型转换构造函数，可以把一个int转换成一个类TEST对象。
	TEST(int a):clsAttr(a){
        cout << "TEST::TEST(int a)构造函数执行！"<< endl;
    }
	TEST(const TEST &a) :clsAttr(a.clsAttr){
        cout << "TEST::TEST(TEST &a)复制构造函数执行！" << endl;
    }
	~TEST(){
        cout << "TEST::~TEST()析构函数执行！" << endl;
    }
};
void myprint1(const int &i, char *pmybuf){
	//通过查看内存可知变量mvar与它的引用mvary地址是相同的，但是传递到myprint()中，
	//&i的地址不是mvar的地址，所以这是个假引用，此时引用传递与传值是一样的。
	//分析可得，并不是mvar的引用，实际是值传递，那么我们认为，即便是主线程detach了子线程，
	//那么子线程用i值任然是安全的!
	cout << i << endl;
	//第二个参数*pmybuf 是指针，*pmybuf的地址与mybuf[]相同；
	//指针在detach子线程时，绝对会有问题；
	cout << pmybuf << endl;
}
//此时用 string& 通过一个隐形转换来接收mybuf[]的值，但此时安全吗？
//但是mybuf是 在什么时候转换成string?
//事实上存在mybuf都被回收了，系统才将mybuf去转string的可能性
//所以此方法也是不安全的
void myprint2(const int i, const string &pmybuf){    
	cout << i << endl;
	cout << pmybuf << endl;
}
int main(){
	int mvar = 1;   
	int &mvary = mvar;
	char mybuf[] = "This is a test!";
	//thread myobj(myprint1, mvar, mybuf);                                
	//string(mybuf)生成临时string对象;我们这里直接将mybuf转换成string对象，
	//这是一个可以保证在线程中用肯定有效的对象。
	//下个程序进行验证
	thread myobj(myprint2, mvar, string(mybuf)); 
	myobj.detach(); 
	cout << "主线程执行！" << endl;
	system("pause");
	return 0;
}
```

 通过自定义类的方式验证临时对象可以保证主线程结束之前，把线程函数的参数构建出来 ;

```c++
//类TEST承接上一程序
void myprint(const int i, const TEST &pmybuf){
	cout << &pmybuf << endl;// 打印的是pmybuf对象的地址
}
int main(){
	int mvar = 1;
	int mysecondpar = 12;
	//我们希望mysecondpar转成TEST类型对象传递给myprint的第二个参数
	/*主线程什么都不操作，快速结束主线程，运行程序后可以发现，
	没有输出“TEST::TEST(int a)构造函数执行！”说明主线程执行完毕，*/
	//thread myobj(myprint, mvar, mysecondpar);
    //若上式运行，不会产生线程myobj,主线程直接结束了，myobj的参数为有效生成（下式注释）
    
	/*在创建线程的同时构造临时对象的方法传递参数是可行的！
	//可以保证在主线程结束之前，构造出来！*/
	thread myobj(myprint, mvar, TEST(mysecondpar));
	myobj.detach(); 
	//cout << "主线程执行！" << endl;
	return 0;
}
/*
TEST::TEST(int a)构造函数执行！
TEST::TEST(TEST &a)复制构造函数执行！
TEST::~TEST()析构函数执行！

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 11492)已退出，代码为 0。
按任意键关闭此窗口. . .
*/
```

### 总结

1. 若传递int这种简单类型参数，建议都是**值传递**，不要引用，防止节外生枝
2. 如果传递类对象，避免隐式类型转换。全部都在创建线程这一行就构建出临时对象，然后在函数参数里用引用来接；否则系统还会多构造一次对象。
3. 终极结论：建议不使用detach(),只使用join();这样就不存在局部变量失效导致线程对内存的非法引用问题。

### 线程ID概念

​		Id是个数字，每一个线程（不管是主线程还是子线程）实际上都对应一个数字，而且每一个线程对应的这个数字都不同。也就是说，不同的线程，他的线程id必然不同；

​		线程ID可以用C++标准库里的函数来获取。**std::this_thread::get_id()来获取**。

```c++
#include<iostream>
#include<thread>
#include<string>
using namespace std;
class A{
public:
	int m_i;
	//类型转换构造函数，可以把一个int转换成一个类A对象。
	A(int a) :m_i(a){
		cout << "A::A(int a)构造函数执行！" << this << "threadid:" << 					std::this_thread::get_id() << endl;
	}
	A(const A& a) :m_i(a.m_i){
		cout << "A::A(A &a)复制构造函数执行！" << this << "threadid:" << 				std::this_thread::get_id() << endl;
	}
	~A(){
		cout << "A::~A()析构函数执行！" << this << "threadid:" << 						std::this_thread::get_id() << endl;
	}
};
void myprint2(const A& pmybuf){
	cout << "子对象myprint的参数地址是" << &pmybuf << "threadid" << 					std::this_thread::get_id() << endl;// 打印的是pmybuf对象的地址
}
int main(){
	cout << "主线程id:" << std::this_thread::get_id() << endl;
	int  mvar = 2;
	//thread myobj(myprint2, mvar); //致命问题是在子线程中构造A类对象
	thread myobj(myprint2, A(mvar)); //用了临时对象后，所有的A类对象都在main()函数中已经构造完毕了
	myobj.join();
	//myobj.detach(); //子线程与主线程分别执行 
	return 0;

}
```

```latex
主线程id:9500
A::A(int a)构造函数执行！008FF7DCthreadid:9500
A::A(A &a)复制构造函数执行！00C0FAE8threadid:9500
A::~A()析构函数执行！008FF7DCthreadid:9500
子对象myprint的参数地址是00C0FAE8threadid12692
A::~A()析构函数执行！00C0FAE8threadid:12692
```

```latex
//执行detach函数结果
主线程id:12408
A::A(int a)构造函数执行！010FFC90threadid:12408
A::A(A &a)复制构造函数执行！012B0DB0threadid:12408
A::~A()析构函数执行！010FFC90threadid:12408
```

#### 传递类对象、智能指针作为线程参数

std::ref()函数的作用 可以实现真正的引用

```c++
//类A同上，mutable int m_i;
void myprint2(const A &pmybuf){
	pmybuf.m_i = 199; //我们修改该值不会影响main()函数
	cout << "子对象myprint的参数地址是" << &pmybuf << "threadid" << std::this_thread::get_id() << endl;// 打印的是pmybuf对象的地址
}
int main(){
	A myobj(10); //生成一个类对象
	thread mytobj(myprint2, std::ref(myobj)); 
	mytobj.join();
	return 0;
}
```

```c++
void myprint2(unique_ptr<int> pzn){
	;
}
int main(){
	unique_ptr<int> myp(new int(100));//unique_ptr为智能指针
	thread mytobj(myprint2,std::move(myp));
	mytobj.join();
	return 0;
}
```

#### 用成员函数指针做线程函数

```c++
#include<iostream>
#include<thread>
#include<string>
using namespace std;
class A{
public:
	mutable int m_i;
	//类型转换构造函数，可以把一个int转换成一个类A对象。
	A(int a) :m_i(a){
		cout << "A::A(int a)构造函数执行！" << this << "threadid:" << std::this_thread::get_id() << endl;
	}
	A(const A &a) :m_i(a.m_i){
		cout << "A::A(A &a)复制构造函数执行！" << this << "threadid:" << std::this_thread::get_id() << endl;
	}
	~A(){
		cout << "A::~A()析构函数执行！" << this << "threadid:" << std::this_thread::get_id() << endl;
	}
	void thread_work(int num){
		cout << "子线程thread——work执行！" << this << "threadid:" << std::this_thread::get_id() << endl;
	}
};
int main(){
	A myobj(10);
	thread mytobj(&A::thread_work, &myobj, 15); 
	mytobj.join();
	return 0;
}
```

#### 多线程运用时机

- 高并发

  系统接受实现多用户多请求的高并发时，通过多线程来实现。

- 线程后台处理大任务

  ​		一个程序是线性执行的。如果程序执行到要花大量时间处理的任务时，那主程序就得等待其执行完才能继续执行下面的。那用户就不得不等待它执行完。

  ​		这时候可以开线程把花大量时间处理的任务放在线程处理，这样线程在后台处理时，主程序也可以继续执行下去，用户就不需要等待。线程执行完后执行回调函数。

- 大任务

  大任务处理起来比较耗时，这时候可以起到多个线程并行加快处理（例如：分片上传）。

​		编写程序过程中需要使用某些阻塞过程时，我们才使用多线程.或者更进一步讲，使用多线程的目的是对阻塞过程中的实际阻塞的抽象提取。