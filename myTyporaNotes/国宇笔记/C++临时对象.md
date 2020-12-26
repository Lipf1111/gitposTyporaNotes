4种常见的临时对象创建的情况：

- 类型装换
- 按值传递
- 按值返回
- 对象定义

#### 1【类型转换】

它通常是为了让函数调用成功而产生临时对象。发生于 “传递某对象给一个函数，而其类型与它即将绑定上去的参数类型不同” 的时候。

例如：

```cpp
void test(const string& str);
char buffer[] = "buffer";
test(buffer); // 此时发生类型转换
```

此时，编译器会帮你进行类型转换：它产生一个类型为string的临时对象，该对象以buffer为参数调用string constructor。当test函数返回时，此临时对象会被自动销毁。

> 注意：对于引用（reference）参数而言，只有当对象被传递给一个reference-to-const参数时，转换才发生。如果对象传递给一个reference-to-non-const对象，不会发生转换。

例如：

```c++
void upper(string& str);
char lower[] = "lower";
upper(lower); // 此时不能转换，编译出错
```

upper函数是将str中所有的字符改为大写，这是作者实现次函数的意图。

但是**以上函数会编译错误**。因为不再有任何临时对象被产生以使函数调用成功。为什么呢？

假设我们编译器为此产生了一个string临时对象，string对象以lower为自变量调用string constructor。然后把此临时对象传递给upper函数，以便将其中的字符全部改为大写。但是函数的实参lower并未受到影响，修改的只是以lower为本产生的那个string临时对象。这就与作者的原始意图不相符了，作者写此函数目的是将lower传递给upper函数，修改lower，但是此函数却不能够完成。

如果编译器针对reference to non const 对象进行隐式类型转换，就会产生以上临时对象被修改而原始实参却不能够修改的窘状。

所以**c++禁止为reference to non const 参数生成临时对象**。

有时候，这种隐式类型转换不是我们期望的，那么我们可以通过声明constructor为explicit来实现。explicit告诉编译器，我们反对将constructor用于类型转换。

例如：

```cpp
explicit string(const char*);
```

#### 2【按值传递】

```c++
void test（T formalArg);

T actualArg;
test(actualArg);
```

实际上就是形参实参问题

#### 3【按值返回】

```c++
 #include <stdio.h>
class Test{
    int mi;
public:
    Test(int i){
        mi = i;
    }
    Test(){ // 这里程序作者想要代码复用，直接调用已经构造好的函数来完成没有参数的构造函数的函数体；
    	Test(0);// 得到临时对象，没有名字，就意味着作用域只在这个语句，过了这个语句，就没法被访问到了；这里的语句在这里没有什么作用，等价于空的语句；
    }
    void print(){
        printf("mi = %d\n", mi);
    }
};
int main(){
    Test t;
    t.print();  // 期望 mi 为 0；但是结果却是随机值；
    return 0;
}
```

3，程序意图：

- 在 Test() 中以 0 作为参数调用 Test(int i)；
- 将成员变量 mi 的初始值设置为 0；

  运行结果：

   成员变量 mi 的值为随机值；

**构造函数是一个特殊的函数：** 

  1，是否可以直接调用？

   给编译器主动调用的，但也可直接手工调用；

  2，是否可以在构造函数中调用构造函数？

​    从编译器的编译结果来看在语法上合法；

  3，直接调用构造函数的行为是什么？

  直接调用构造函数将会产生一个临时对象；

- 是一个合法的 C++ 对象，生命期只有一条语句时间；
- 过了这个语句临时对象就会被自动析构而不复存在；
- 临时对象是没有名字的；
- 临时对象的生命周期只有一条语句；
- 临时对象的作用域只在一条语句中；
- 临时对象是 C++ 中值得警惕的灰色地带；

避免因代码复用调用构造函数而带来的临时对象的解决方案：

```c++
#include <stdio.h>
class Test {
    int mi;
    void init(int i){
        mi = i;
    }
public:
    Test(int i){
        init(i);
    }
    Test(){
        init(0);  // 工程中代码复用方式
    }
    void print() {
        printf("mi = %d\n", mi);
    }
};
int main(){
    Test t;
    t.print();
    return 0;
}
```

临时对象的测试：

```c++
 #include <stdio.h>
class Test {
    int mi;
    void init(int i){
        mi = i;
    }
public:
    Test(int i){
        printf("Test(int i)\n");
        init(i);
    }
    Test(){
        printf("Test()\n");
        init(0);
    }
    void print() {
        printf("mi = %d\n", mi);
    }
    ~Test(){
        printf("~Test()\n");
    }
};
int main(){
    printf("main begin1\n");
    Test();  // 产生临时对象；打印 ==> Test() ~Test()
    Test(10);  // 产生临时对象；打印 ==> Test(int i) ~Test()
    printf("main end1\n");
    printf("main begin2\n");
    Test().print();  // 临时对象生成之后直接调用 print() 函数；这里可以通过编译，因为调用了构造函数之后呢就会产生临时对象了，通过合法的 C++ 对象加上点操作符来调用对应的成员函数是完全没有问题，完全合法的；产生临时对象；打印 ==> Test()    mi = 0    ~Test()
    Test(10).print(); //产生临时对象；打印 ==> Test(int i) mi = 10 ~Test()
    printf("main end2\n");
    return 0;
}
```

一定要去避免临时对象的产生和使用；

现代 C++ 编译器在不影响最终执行结果的前提下，会尽力减少临时对象的产生；

**神秘的临时对象编程实验：**

  证明 C++ 编译器在极力的减少临时对象的产生；

```c++
#include <stdio.h>
class Test{
    int mi;
public:
    Test(int i){
        printf("Test(int i) : %d\n", i);
        mi = i;
    }
    Test(const Test& t){
        printf("Test(const Test& t) : %d\n", t.mi);
        mi = t.mi;
    }
    Test(){
        printf("Test()\n");
        mi = 0;
    }
    void print(){
        printf("mi = %d\n", mi);
    }
    ~Test(){
        printf("~Test()\n");
    }
};
Test func(){
    return Test(20);  // 生成一个临时对象，函数调用结束后就立即销毁临时对象了
}
int main(){
    //Test t(10); // 等价于 Test t = Test(10);  #1
    Test t = Test(10); // ==> Test t = 10;   #2
    t.print();// 打印 mi = 10;
    Test tt = func();  // ==> Test tt = Test(20);     #3
    tt.print();  // 打印 mi = 20；
    return 0;
}
```

#1

于是可解读为：
    1，生成临时对象 
    2，用临时对象初始化,即将生成的t对象,于是必然涉及到调用拷贝构造函数.
    但是编译器打印的结果为

```out
Test(int i) ： 10
 ~Test()
```

根本没有任何拷贝构造函数的迹象产生；
    编译器根本没有像我们解读的上述的两个步骤执行；这是因为现代的 C++ 编译器都会尽量的减少临时对象的产生；
    从执行结果来看，上面的分析是没有任何错误的，只是上面的还要再调用一次拷贝构造函数，通过上面的分析，即便结果没有任何的变化，但是效率降低了，因此在这个地方 C++ 编译器为了杜绝临时对象的产生，直接将Test t = Test(10) 等价成为了 Test t = 10，这样就杜绝了临时对象的产生；
    杜绝临时对象的产生是因为其往往会带来性能上面的题，先生成一个临时对象调用了一次构造函数，再将临时对象通过拷贝构造函数来初始化 t，也就是说调用了两次构造函数，如果说我们极力的减少临时对象的产生，那么通过上述第二个方式等价 ，这样调用一次构造函数就可以了，少调用了一次拷贝构造函数，性能就提升了；
    在这个地方从性能上我们没有多大体会，但在实际的工程 开发中，构造函数里面所做的初始化工作往往是纷繁复杂的，比方说有些类的对象在初始化的时候，在调用构造函数的时候很可能打开外部设备，对设备进行初始设置等等，如果这个时候多了一次构造函数的调用，时间就消耗了；
    为什么要这样呢？因为 C++ 天生继承了 C 的特性，C 的一个最大特性就是高效，所以 C++ 的一个特性也是要极力的做到高效，该省掉临时对象的地方，就该省掉。

单独运行#1，#2的结果相同

#3 

fun() 返回一个临时对象，到达了赋值符号右侧，此时临时对象里面的值会初始化 t 对象；当代的 C++ 编译器，在不影响最终结果的情况下，会极力的减少临时对象的产生；

```c++
#include <iostream>
using namespace std;

class A {
public:
	int m_k;
	int m_t;
	A(int k, int t) :m_k(k), m_t(t) {
		cout << "construct...." << endl;
	}
	~A() {
		cout << "destruct...." << endl;
	}
	A(A &a) {
		cout << "copy construct..." << endl;
	}
};

//  如果函数返回值是一个对象，要考虑return语句的效率
A getObj() {
/************** 返回本地对象 ****************/	
    /* 以下这种写法实际上执行了三步:
    1. 构造本地对象a
    2. 调用拷贝构造，将本地对象a拷贝到外部存储器
    3. 调用析构函数析构本地对象a
    */
/******************************************/
    A a(3, 4);
    return a;
}
int main() {
    getObj();   //  外部存储单元
    return 0;
}
```

```out
construct....
copy construct...
destruct....
destruct....
```

```c++
#include <iostream>
using namespace std;

class A {
public:
	int m_k;
	int m_t;
	A(int k, int t) :m_k(k), m_t(t) {
		cout << "construct...." << endl;
	}
	~A() {
		cout << "destruct...." << endl;
	}
	A(A && a) {//注意此处为&&，即右值引用，若为&则报：由于复制构造函数不明确或没有可用的复制构造函数，因此无法复制构造 class“A”  注意与上例区分
		cout << "copy construct..." << endl;
	}
};
//  如果函数返回值是一个对象，要考虑return语句的效率
A getObj() {
	/***********  直接返回临时对象  *************/
	// 编译器直接把临时对象创建并初始化在外部
	// 存储单元(主调函数的栈帧上)中，省去了拷
	// 贝和析构的花费，提高了效率
	/*****************************************/
	return A(3, 4);  
    //此处产生临时对象，函数返回值为对象，所以要用右值引用
}
int main() {
	getObj();   //  外部存储单元
	return 0;
}	
```

```out
construct....
destruct....
```

```
 #include <iostream>
using namespace std;

class A {
public:
	int m_k;
	int m_t;
	A(int k, int t) :m_k(k), m_t(t) {
		cout << "construct...." << endl;
	}
	~A() {
		cout << "destruct...." << endl;
	}
	A(A& a) {
		cout << "copy construct..." << endl;
	}
	A(A&& a) {
		cout << "copy rValue construct..." << endl;
	}
};
A getObj1() {
	return A(3, 4);
}
A getObj2() {
	A a(3, 4);
	return a;
}
int main() {
	getObj1();   
	getObj2();   
	return 0;
}
```

```out
construct....
destruct....
construct....
copy rValue construct...
destruct....
destruct....
```



		1. 构造本地对象a
		2. 调用拷贝构造，将本地对象a拷贝到外部存储器
		3. 调用析构函数析构本地对象a
			*/
			/******************************************/
	A a(3, 4);
	return a;
}
int main() {
	getObj1();   //  外部存储单元
	getObj2();   //  外部存储单元
	return 0;
}

**小结：**

1. 直接调用构造函数将产生一个临时对象；
2. 临时对象是性能的瓶颈，也是 bug 的来源之一；；

	- C++ 中除了野指针，还有临时对象；

	- 工作中，如果出现了奇怪的问题，要从这两个方面考虑；

3. 现代 C++ 编译器会尽力避开临时对象；

	- 避开的前提是不影响最终的执行结果；

4. 实际工程开发中需要人为的避开临时对象；

	- 避免调用构造函数来初始化；
	- 设计函数不要引入像 fun() 函数中的临时对象；

​		函数在返回自定义类型时，我们知道主调函数会为返回值开辟一个临时内存来存储这个返回值对象，但是在返回对象的时候也有一些特殊的情况，Main函数不会为返回的对象开辟内存。 

#### 4【对象定义】

```c++
Integer i1(100); // 编译器肯定不会生成临时对象
Integer i2 = Integer(100); // 编译器可能生成临时对象
Integer i3 = 100; // 编译器可能生成临时对象
```

 然而，实际上大多数的编译器都会通过优化省去临时对象，所以这里的初始化形式基本上在效率上都是相同的。 