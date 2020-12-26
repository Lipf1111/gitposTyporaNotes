##### 右值引用

能出现在赋值号左边的表达式称为“左值”，不能出现在赋值号左边的表达式称为“右值”。一般来说，左值是可以取地址的，右值则不可以。

非 const 的变量都是左值。函数调用的返回值若不是引用，则该函数调用就是右值。前面所学的“引用”都是引用变量的，而变量是左值，因此它们都是“左值引用”。

C++11 新增了一种引用，可以引用右值，因而称为“右值引用”。无名的临时变量不能出现在赋值号左边，因而是右值。右值引用就可以引用无名的临时变量。定义右值引用的格式如下： 

` 类型 && 引用名 = 右值表达式; `

```c++
class A{};
A & rl = A();  //错误，无名临时变量 A() 是右值，因此不能初始化左值引用 r1
A && r2 = A();  //正确，因 r2 是右值引用
```

引入右值引用的主要目的是**提高程序运行的效率**。有些对象在复制时需要进行深复制，深复制往往非常耗时。合理使用右值引用可以避免没有必要的深复制操作。例如下面的程序： 

```c++
#include <iostream>
#include <string>
#include <cstring>
using namespace std;
class String{
public:
    char* str;
    String() : str(new char[1]) { str[0] = 0; }
    String(const char* s) {
        str = new char[strlen(s) + 1];
        strcpy(str, s);
    }
    String(const String & s) {//复制构造函数
        cout << "copy constructor called" << endl;
        str = new char[strlen(s.str) + 1];
        strcpy(str, s.str);
    }
    String & operator = (const String & s) {//复制赋值号
        cout << "copy operator = called" << endl;
        if (str != s.str) {
            delete[] str;
            str = new char[strlen(s.str) + 1];
            strcpy(str, s.str);
        }
        return *this;
    }
    String(String && s) : str(s.str) { //移动构造函数,移动之后s.str置空
        cout << "move constructor called" << endl;
        s.str = new char[1];
        s.str[0] = 0;
    }
    String & operator = (String && s) { //移动赋值号
        cout << "move operator = called" << endl;
        if (str != s.str) {
            str = s.str;
            s.str = new char[1];
            s.str[0] = 0;
        }
        return *this;
    }
    ~String() { delete[] str; }
};
template <class T>
void MoveSwap(T & a, T & b) {
    T tmp(move(a));  //std::move(a) 为右值，这里会调用移动构造函数
    a = move(b);  //move(b) 为右值，因此这里会调用移动赋值号
    b = move(tmp);  //move(tmp) 为右值，因此这里会调用移动赋值号
}
int main(){
    String s;
    s = String("this");  //调用移动赋值号
    cout << "* * * *" << endl;
    cout << s.str << endl;
    String s1 = "hello", s2 = "world";
    MoveSwap(s1, s2);  //调用一次移动构造函数和两次移动赋值号
    cout << s2.str << endl;
    return 0;
}
```

```out
 程序的输出结果如下：
move operator = called
****
this
move constructor called
move operator = called
move operator = called
hello 
```

 第 33 行重载了一个移动赋值号。它和第 19 行的复制赋值号的区别在于，其参数是右值引用。在移动赋值号函数中没有执行深复制操作，而是直接将对象的 str 指向了参数 s 的成员变量 str 指向的地方，然后修改 s.str 让它指向别处，以免 s.str 原来指向的空间被释放两次。

该移动赋值号函数修改了参数，这会不会带来麻烦呢？答案是不会。因为移动赋值号函数的形参是一个右值引用，则调用该函数时，实参一定是右值。右值一般是无名临时变量，而无名临时变量在使用它的语句结束后就不再有用，因此其值即使被修改也没有关系。

第 53 行，如果没有定义移动赋值号，则会导致复制赋值号被调用，引发深复制操作。临时无名变量`String("this")`是右值，因此在定义了移动赋值号的情况下，会导致移动赋值号被调用。移动赋值号使得 s 的内容和 String("this") 一致，然而却不用执行深复制操作，因而效率比复制赋值号高。

虽然移动赋值号修改了临时变量 String("this")，但该变量在后面已无用处，因此这样的修改不会导致错误。

第 46 行使用了 C++ 11 中的标准模板 move。move 能接受一个左值作为参数，返回该左值的右值引用。因此本行会用定义于第 28 行、以右值引用作为参数的移动构造函数来初始化 tmp。该移动构造函数没有执行深复制，将 tmp 的内容变成和 a 相同，然后修改 a。由于调用 MoveSwap 本来就会修改 a，所以 a 的值在此处被修改不会产生问题。

第 47 行和第 48 行调用了移动赋值号，在没有进行深复制的情况下完成了 a 和 b 内容的互换。对比 Swap 函数的以下写法： 

```c++
template <class T>
void Swap(T & a, T & b) {
    T tmp(a);  //调用复制构造函数
    a=b;  //调用复制赋值号
    b=tmp;  //调用复制赋值号
}
```

Swap 函数执行期间会调用一次复制构造函数，两次复制赋值号，即一共会进行三次深复制操作。而利用右值引用，使用 MoveSwap，则可以在无须进行深复制的情况下达到相同的目的，从而提高了程序的运行效率。 

##### 移动语义

 c++11中引入了`移动语义`，可以避免无谓的复制，提高程序性能。 RVO（Return Value Optimization）是一种编译器优化机制：当函数需要返回一个对象的时候，如果自己创建一个临时对象返回，那么这个临时对象会消耗一个构造函数（Constructor）、一个拷贝构造函数（Copy Constructor）以及一个析构函数（Destructor）的调用的代价，RVO的目的就是消除为保存返回值而创建的临时对象，这样就可以将成本降低到一个构造函数的代价。更具体的请自行查阅资料。

言归正传，直奔主题，移动构造函数中为指针成员分配新的内存再进行内容拷贝的做法在C++编程中几乎被视为不可违背的，不过在一些时候，我么确实不需要这样的拷贝构造语义。我们看看下面的例子。

```cpp
class HasPtrMem{
    public:	HasPtrMem() : d(new int(0))	{
        cout << "Construct：" << ++n_cstr << endl;
    };
    HasPtrMem(const HasPtrMem&h) : d(new int(*h.d))	{
        cout << "Copy Construct：" << ++n_cptr << endl;
    }
    ~HasPtrMem()	{
        delete d;
        cout << "Destruct：" << ++n_dstr << endl;
    }
    int *d;
    static int n_cstr;
    static int n_cptr;
    static int n_dstr;
};
int HasPtrMem::n_cptr = 0;
int HasPtrMem::n_cstr = 0;
int HasPtrMem::n_dstr = 0;
HasPtrMem GetTemp(){
    HasPtrMem h;
    return h;
} 
int main(){
    HasPtrMem a = GetTemp();
}
```

在上例中，我们声明了一个返回一个HasPtrMem变量的函数。为了记录构造函数、拷贝构造函数，以及析构函数调用的次数，我们使用了一些静态变量。在main函数中，我们简单地声明了一个HasPtrMem的变量a，要求它使用GetTemp的返回值进行初始化。

编译程序，我们看到下面的输出：

优化前：

```cpp
Construct：1
Copy Construct：1
Destruct：1
Copy Construct：2
Destruct：2
Destruct：3
```

 优化后：

```cpp
Construct：1
Copy Construct：1
Destruct：1
Destruct：2
```

这里主要分析优化前的结果，这里构造函数被调用了一次，这是在GetTemp函数中HasPtrMem()表达式显式地调用了构造函数而打印出来的。而拷贝构造函数则被调用了两次。第一次是从GetTemp函数中HasPtrMem()生成的变量上拷贝构造出一个临时值，以用作GetTemp的返回值，而另外一次则是由临时值构造出main中变量a调用的。对应地，析构函数也就被调用3次。这个过程如下图。

![img](.\Pic\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3F1aW50YV8y.jpg)

最让人感到不安就是拷贝构造函数的调用。在我们的例子里，类HasPtrMem只有一个int类型的指针。而如果HasPtrMem的指针指向非常大的堆内存数据的话，那么拷贝构造的过程就会非常昂贵。

让我们把目光再次聚集在临时对象上，即在main函数的部分。按照C++的语义，临时对象将在语句结束后被析构，会释放它所包含的堆内存资源。而a在拷贝构造的时候，又会被分配堆内存。这样的一去一来似乎并没有多大的意义，那么我们是否可以在临时对象构造a的时候不分配内存，即不适用所谓的拷贝语义呢？

在C++11中，答案是肯定的。我们可以看看下图。

![img](.\Pic\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3.jpg)

上半部分可以看到从临时变量中拷贝构造变量a的做法，即在拷贝时分配新的堆内存，并从临时对象的堆内存中拷贝内容。而构造完成后，临时对象将析构，因此其拥有的堆内存资源会被析构函数释放。而下半部分则是一种“新”方法，该方法在构造时使得a.d指向临时对象的内内存资源。同时我们保证临时对象不释放所指向的堆内存，那么在构造完成后，临时对象对象被析构，a就从中“偷”到了临时对象所拥有的堆内存资源。

在C++11中，这样的“偷走”临时变量中资源的构造函数，就被称为“移动构造函数”，而这样的“偷”的行为，则称之为“移动语义”。我们看看如何实现这种移动语义，如下图。

```cpp
class HasPtrMem{
public:	
    HasPtrMem() : d(new int(0))	{
        cout << "Construct：" << ++n_cstr << endl;
    };
    HasPtrMem(const HasPtrMem&h) : d(new int(*h.d))	{
        cout << "Copy Construct：" << ++n_cptr << endl;
    }
    HasPtrMem(HasPtrMem &&h) : d(h.d)	{
        h.d = nullptr;
        cout << "Move Construct：" << ++n_mvtr << endl;
    }
    ~HasPtrMem()	{
        delete d;
        cout << "Destruct：" << ++n_dstr << endl;
    }
    int *d;
    static int n_cstr;
    static int n_cptr;
    static int n_mvtr;
    static int n_dstr;
};
int HasPtrMem::n_cptr = 0;
int HasPtrMem::n_cstr = 0;
int HasPtrMem::n_mvtr = 0;
int HasPtrMem::n_dstr = 0;
HasPtrMem GetTemp(){
    HasPtrMem h;
    cout << "Resource from" << __func__ << ":" << hex << h.d << endl;
    return h;
}
int main(){
    HasPtrMem a= GetTemp();
    cout << "Resource from" << __func__ << ":" << hex << a.d << endl;
    return 0;
}
```

相比于之前的代码，上例HasPtrMem类多了一个构造函数HasPtrMem（HasPtrMem&&），这个就是我们所谓的移动构造函数。与拷贝构造函数不同的是，移动构造函数接受一个所谓的“右值引用”的参数。可以看到，移动构造函数使用了参数h的成员d初始化了本对象的成员d（而不是像拷贝构造函数一样需要分配内存，然后将内容依次拷贝到新分配的内存中），而h的成员d随后被置为指针空值nullptr。这就完成了移动构造的全过程。

这里所谓的“偷”对内存，就是指将本对象d指向h.d所指的内存这一条语句，相应地，我们还将h的成员d置为指针空值。这其实也是我们“偷”内存是必须做的。不然，在移动构造完成之后，临时对象会立即被析构。如果不改变h.d的话，则临时对象会析构掉本是我们“偷”来得堆内存。这样一来，就造成了悬挂指针。

为了看看移动构造函数的效果，我们让GetTemp和main函数分别打印h和变量a中的指针h.d和a.d，我们的结果如下：

优化前：

```
Construct：1
Resource fromGetTemp:0000015A20CF1FE0
Move Construct：1
Destruct：1
Move Construct：2
Destruct：2
Resource frommain:0000015A20CF1FE0
Destruct：3
```

优化后：

```cpp
Construct：1
Resource fromGetTemp:0000015A20CF1FE0
Move Construct：1
Destruct：1
Resource frommain:0000015A20CF1FE0
Destruct：2
```