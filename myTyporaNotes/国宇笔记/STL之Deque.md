# deque函数：

   deque容器为一个给定类型的元素进行**线性处理**，像向量一样，它能够快速地随机访问任一个元素，并且能够高效地插入和删除容器的尾部元素。但它又与vector不同，deque支持高效插入和删除容器的头部元素，因此也叫做双端队列。deque类常用的函数如下。

(1)  构造函数

deque():创建一个空deque

deque(int nSize):**创建一个deque,元素个数为nSize**

deque(int nSize,const T& t):创建一个deque,元素个数为nSize,且值均为t

deque(const deque &):复制构造函数

(2)  增加函数

void **push_front(const T& x):双端队列头部增加一个元素X**

void ***\*push_back(const T& x):双端队列尾部增加一个元素x\****

iterator **insert**(iterator it,const T& x):双端队列中***\*某一元素前\****增加一个元素x

void **insert**(iterator it,int n,const T& x):双端队列中***\*某一元素前\****增加n个相同的元素x

void **insert**(iterator it,const_iterator first,const_iteratorlast):双端队列中***\*某一元素前\****插入另一个相同类型向量的[forst,last)间的数据

(3)  删除函数

Iterator **erase**(iterator it):删除双端队列中的***\*某一个元素\****

Iterator **erase**(iterator first,iterator last):删除双端队列中[first,last）中的元素

void pop_front():删除双端队列中***\*最前一个元素\****

void pop_back():删除双端队列中***\*最后一个元素\****

void clear(): 移除deque的所有元素（并且销毁）,使容器大小为0. 

(4)  遍历函数

reference at(int pos):返回pos位置元素的引用

reference **front**():返回***\*首元素的引用\****

reference **back**():返回***\*尾元素的引用\****

iterator ***\*begin\****():返回向量头指针，指向第一个元素

iterator ***\*end\****():返回指向向量中最后一个元素下一个元素的指针（不包含在向量中）

reverse_iterator **rbegin**():反向迭代器，指向最后一个元素

reverse_iterator rend():反向迭代器，指向第一个元素的前一个元素

(5)  判断函数

bool empty() const:向量是否为空，若true,则向量中无元素

(6)  大小函数

Int **size()** const:返回向量中元素的个数

int **max_size()** const:返回最大可允许的双端对了元素数量值

(7)  其他函数

void swap(deque&):交换两个同类型向量的数据

void assign(int n,const T& x):向量中第n个元素的值设置为x

#### 操作示例：

```c++
// deque.cpp : 定义控制台应用程序的入口点。
//

#include "stdafx.h"
#include<iostream>
#include<deque>

using namespace std;
int _tmain(int argc, _TCHAR* argv[])
{
	deque<int> d;
	d.push_back( 10 );
	d.push_back(20);
	d.push_back(30);
	cout<<"原始双端队列："<<endl;
	for(int i = 0; i < d.size(); i++)
	{
		cout<<d.at(i)<<"\t";
	}
	cout<<endl;
	d.push_front(5);
	d.push_front(3);
	d.push_front(1);

	cout<<"after push_front(5.3.1):"<<endl;
	for(int i = 0;i < d.size();i++)
	{
		cout<<d.at(i)<<"\t";
	}
	cout<<endl;
	d.pop_front();
	d.pop_front();
	cout<<"after pop_front() two times:"<<endl;
	for(int i = 0;i < d.size();i++)
	{
		cout<<d.at(i)<<"\t";
	}
	cout<<endl;
	return 0;

}
```

程序运行结果如下所示：

![img](.\Pic\deque.jpg)

####  2.deque与vector内存分配比较： 

```c++
// deque.cpp : 定义控制台应用程序的入口点。
//

#include "stdafx.h"
#include<iostream>
#include<deque>
#include<vector>

using namespace std;
int _tmain(int argc, _TCHAR* argv[])
{
	vector<int>v(2);
	v[0]=10;
	int *p = &v[0];
	cout<<"vector第一个元素迭代指针*p="<<*p<<endl;
	v.push_back(20);
	cout<<"vector容量变化后原vector第1个元素迭代指针*p="<<*p<<endl;

	deque<int> d(2);
	d[0]=10;
	int *q = &d[0];
	cout<<"deque第一个元素迭代指针*q="<<*q<<endl;
	d.push_back(20);
	cout<<"deque容量变化后第一个元素迭代器指针*q="<<*q<<endl;

}
```

程序运行结果如下图所示

![img](.\Pic\dequeandvector.jpg)

该段程序的功能是：deque、vector初始化后大小为2，第一个元素都为10，当通过push_back函数分别给两个容器增加一个元素后，从结果发现原先保持的指针元素值对vector容器前后发生了变化，而对deque容器前后没有发生变化。

原因为，在建立vector容器时，一般来说伴随这建立空间->填充数据->重建更大空间->复制原空间数据->删除原空间->添加新数据，如此反复，保证vector始终是一块独立的连续内存空间；在建立deque容器时，一般便随着建立空间->建立数据->建立新空间->填充新数据，如此反复，没有原空间数据的复制和删除过程，是由多个连续的内存空间组成的。 

传送门： https://blog.csdn.net/u011630575/article/details/79923132 