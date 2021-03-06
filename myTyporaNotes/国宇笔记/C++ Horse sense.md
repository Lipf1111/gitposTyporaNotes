### **C++ Horse sense**

#### Q1 分号问题

```c++
namespace this_thread {//自定义命名空间时
}
```

后面不加分号“；”

```c++
class test{
};
```

后面加分号“；”

#### Q2 char\*与char[]

```c++
char *str1 = "abc";//str1指向的是字符串"abc"的存储区,是const的
char str1[] = "abc";//则str1分配了存储空间来存储"abc"是可变的//栈区
```

#### Q3 qsort与sort

void **qsort**(void \*base,int nelem,int width,int (\*fcmp)(const void\*,const void \*)); 

```c++
int cmp(const void *a ,const void *b){
	return *(int *)a - *(int *)b ;	//从小到大排序，把a，b位置反过来就是从大到小 
}
//...
int a[10]={-1,9,5,7,-11,2,6,8,9,6};
qsort(a,10,sizeof(int),cmp);
//...
```

不可换成<&>号(引用), 属于C语言标准库函数 . 因为C语言没有明确的定义bool类型，只是笼统的说，零为假，任何非零都是真，而==qsort的cmp函数是返回int==的。 

void **sort**( iterator start, iterator end, StrictWeakOrdering cmp );下面是标准声明：

```c++
template <class RandomAccessIterator, class Compare>
void sort (RandomAccessIterator first, RandomAccessIterator last, Compare comp);
```

他的头文件是<algorithm>，这个是标准C++头文件里的

```c++
bool cmp(int a ,int b){
	return a < b ;		//从小到大排序，把 < 换成 > 就是从大到小 
}
...
int a[10]={-1,9,5,7,-11,2,6,8,9,6};
sort(a,a+10,cmp);
...
```

#### Q4 const char赋值

  const char Name[]    用strcpy(name,Name); 

#### Q5 C标准库-<ctype.h>

| 函数                   | 描述                                     |
| :--------------------- | :--------------------------------------- |
| int **isalnum**(intc)  | 该函数检查所传的字符是否是字母和数字。   |
| int **isalpha**(int c) | 该函数检查所传的字符是否是字母。         |
| int iscntrl(int c)     | 该函数检查所传的字符是否是控制字符。     |
| int **isdigit**(int c) | 该函数检查所传的字符是否是十进制数字。   |
| int isgraph(int c)     | 该函数检查所传的字符是否有图形表示法。   |
| int **islower**(int c) | 该函数检查所传的字符是否是小写字母。     |
| int **isprint**(int c) | 该函数检查所传的字符是否是可打印的。     |
| int ispunct(int c)     | 该函数检查所传的字符是否是标点符号字符。 |
| int **isspace**(int c) | 该函数检查所传的字符是否是空白字符。     |
| int **isupper**(int c) | 该函数检查所传的字符是否是大写字母。     |
| int isxdigit(int c)    | 该函数检查所传的字符是否是十六进制数字。 |
| int **tolower**(int c) | 该函数把大写字母转换为小写字母。         |
| int **toupper**(int c) | 该函数把小写字母转换为大写字母。         |

| 字符类       | &描述                                                        |
| :----------- | :----------------------------------------------------------- |
| 数字         | 完整的数字集合 { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 }              |
| 十六进制数字 | 集合 { 0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f }         |
| 小写字母     | 集合 { a b c d e f g h i j k l m n o p q r s t u v w x y z } |
| 大写字母     | 集合 {A B C D E F G H I J K L M N O P Q R S T U V W X Y Z }  |
| 字母         | 小写字母和大写字母的集合                                     |
| 字母数字字符 | 数字、小写字母和大写字母的集合                               |
| 标点符号字符 | 集合 ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { \| } ~ |
| 图形字符     | 字母数字字符和标点符号字符的集合                             |
| 空格字符     | 制表符、换行符、垂直制表符、换页符、回车符、空格符的集合。   |
| 可打印字符   | 字母数字字符、标点符号字符和空格字符的集合。                 |
| 控制字符     | 在 ASCII 编码中，这些字符的八进制代码是从 000 到 037，以及 177（DEL）。 |
| 空白字符     | 包括空格符和制表符。                                         |
| 字母字符     | 小写字母和大写字母的集合。                                   |

#### Q6 数组分配空间

new int[] 是创建一个int型数组，数组大小是在[]中指定

例如：
int * p = new int[10]; //p执行一个长度为10的int数组。

new int()是创建一个int型数，并且用()括号中的数据进行初始化

例如：
int *p = new int(10); // p指向一个值为10的int数。

#### Q7 补码

**正数的补码，是其本身。**

**负数的补码有两种方式求得：**

1. 就用它的正数得原码，减一取反，即可得到补码。
2. 原码，取反得反码，反码+1得补码

如，已知：＋9 补码是：0000 1001。

下面求－9 补码：

- 先减一：0000 1001 - 1 = 0000 1000；

​       再取反：1111 0111。

- 先取反：1111 0110

  在加一：1111 0111

所以有：－9 补码 = 1111 0111。

#### Q8 typora图片居中问题

对于Typora图片居中问题：在"![](***.png)[回车]"紧挨着的后面敲击回车键即可将图片居中

#### Q9 代码：-1073741510

又是Code::Blocks,老显示Process terminated with status -1073741510，今天查了一下。

-1073741510就是0xC000013A，也就是STATUS_CONTROL_C_EXIT。

应该是运行中按CTRL+C终止程序运行时返回这个值。

However this exit code simply means that the CMD Prompt Windows was closed during execution of the script.

表示命令窗口在程序执行过程中被关闭。

#### Q10 sort的lambda函数

sort(vec.begin(), vec.end(), \[](const structPara& p1, const structPara& p2) {
		return p1.d < p2.d;
		});

#### Q11 结构体定义时构造

```c++
 struct Edge{  
	int from, to, cap, flow; 
    Edge(int from, int to,intcap,intflow):from(from),to(to),cap(cap),flow(flow){
        //...
    } 
 }; 
```

#### Q12 vector遍历方法

1.迭代器

```cpp
std::vector<int>::iterator it;
for (it = vecTest.begin(); it != vecTest.end(); ++it){
    tempNum = *it;                               
}
```

2.C++11 新增关键字auto

```cpp
for (auto it : vecTest){
    tempNum = it;
}
```

3.对C念念不舍的童鞋们习惯的数组写法

```cpp
for (size_t i = 0; i < maxCount; i++){
    tempNum = vecTest[i];
}
```

#### Q13 结构体初始化

定义

```
struct InitMember{
    int first；
    double second；
    char* third；
    float four;
};
```

 方法一：定义时赋值

```
struct InitMember test = {-10,3.141590,"method one",0.25};
```

需要注意对应的顺序，不能错位。

方法二：定义后逐个赋值

```
struct InitMember test;

test.first = -10;
test.second = 3.141590;
test.third = "method two";
test.four = 0.25;123456
```

因为是逐个确定的赋值，无所谓顺序啦。

方法三：定义时乱序赋值（C风格）

这种方法类似于第一种方法和第二种方法的结合体，既能初始化时赋值，也可以不考虑顺序；

```
struct InitMember test = {
    .second = 3.141590,
    .third = "method three",
    .first = -10,
    .four = 0.25
};
```

这种方法在==Linux内核（kernel）==中经常使用，在音视频编解码库FFmpeg中也大量频繁使用，还是很不错的一种方式。

方法四：定义时乱序赋值（C++风格）

这种方法和前一种类似，网上称之为C++风格，类似于key-value键值对的方式，同样不考虑顺序。

```
struct InitMember test = {
    second：3.141590,
    third："method three",
    first：-10,
    four：0.25
};
```

**使用库函数**

 常用的函数有两个：memset和bzero。用法如下：

```cpp
memset(&test, 0, sizeof (test));
bzero(&test, sizeof (test)); 
```

memset和bzero的主要区别是：参数个数不同；memset 需要三个参数，其中第二个参数是&st指向的内存中要初始化的值，而bzero使用0来初始化

#### Q14 常见define

```c++
#define INF         (~(0x1<<31))        // 最大值(即0X7FFFFFFF)
#define isLetter(a) ((((a)>='a')&&((a)<='z')) || (((a)>='A')&&((a)<='Z')))
#define LENGTH(a)   (sizeof(a)/sizeof(a[0]))
```

#### Q15 敏捷开发

​	敏捷开发以用户的需求进化为核心，采用迭代、循序渐进的方法进行软件开发。在敏捷开发中，软件项目在构建初期被切分成多个子项目，各个子项目的成果都经过测试，具备可视、可集成和可运行使用的特征。换言之，就是把一个大项目分为多个相互联系，但也可独立运行的小项目，并分别完成，在此过程中软件一直处于可使用状态。 

####  Q16 内部存储负数和浮点数

负数比较容易，就是通过一个标志位和补码来表示。
对于浮点类型的数据采用单精度类型（float）和双精度类型(double)来存储，float数据占用32bit,double数据占用64bit,我们在声明一个变量float f= 2.25f的时候，是如何分配内存的呢？如果胡乱分配，那世界岂不是乱套了么，其实不论是float还是double在存储方式上都是遵从IEEE的规范的，float遵从的是IEEE R32.24 ,而double 遵从的是R64.53。更多可以参考浮点数表示。
无论是单精度还是双精度在存储中都分为三个部分：

- 符号位(Sign) : 0代表正，1代表为负
- 指数位（Exponent）:用于存储科学计数法中的指数数据，并且采用移位存储
- 尾数部分（Mantissa）：尾数部分
  其中float的存储方式如下图所示：
  ![在这里插入图片描述](.\Pic\70)
  而双精度的存储方式如下图:
  ![在这里插入图片描述](.\Pic\701)

#### Q17 函数调用的过程

```c++
int main(void)
{
  ...
  d = fun(a, b, c);
  cout<<d<<endl;
  ...
  return 0;
}
```

调用fun()的过程大致如下：

- main()========
- 参数拷贝（压栈），注意顺序是从右到左，即c-b-a；
- 保存d = fun(a, b, c)的下一条指令，即cout<<d<<endl（实际上是这条语句对应的汇编指令的起始位置）;
- 跳转到fun()函数，注意，到目前为止，这些都是在main()中进行的；
- fun()=====
- 移动ebp、esp形成新的栈帧结构;
- 压栈（push）形成临时变量并执行相关操作;
- return一个值;
- 出栈（pop）;
- 恢复main函数的栈帧结构;
- 返回main函数;
- main()========
- ……

#### Q18 C函数不限定返回值

在C语言中，凡不加返回值类型限定的函数，就会被编译器作为返回==整型值==处理而不是void类型。

#### Q19 void\*类型的指针

void是一种特殊的指针类型，可以用来存放任意对象的地址。一个void指针存放着一个地址，这一点和其他指针类似。不同的是，我们对它到底储存的是什么对象的地址并不了解；

```c++
double a=2.3;
int b=5;
void *p=&a;
cout<<p<<endl;  //输出了a的地址
p=&b;
cout<<p<<endl;  //输出了b的地址
//cout<<p<<endl;这一行不可以执行，void指针只可以储存变量地址，不冷直接操作它指向的对象
```

利用void\*可以直接做的事比较有限：

- 拿他和别的指针比较
- 作为函数的输入或者输出
- 赋值给另外一个void\*的指针*
- 不可以操作void\*指向的对象

#### Q20 extern“C”

在C++程序中调用被C编译器编译后的函数，为什么要加extern“C”?

C++语言支持函数重载，C语言不支持函数重载，函数被C++编译器编译后在库中的名字与C语言的不同，假设某个函数原型为：

```c++
void foo(int x, int y);
```

该函数被C编译器编译后在库中的名字为 _foo, 而C++编译器则会产生像: _foo_int_int 之类的名字。为了解决此类名字匹配的问题，C++提供了C链接交换指定符号 extern “C”。

#### Q21 防止头文件被重复包含

头文件中的` ifndef/define/endif` 是干什么用的? 该用法和 `program once `的区别？

**相同点:**
	它们的作用是防止头文件被重复包含。
**不同点**

- ifndef 由语言本身提供支持，但是 program once 一般由编译器提供支持，也就是说，有可能出现编译器不支持的情况(主要是比较老的编译器)。
- 通常运行速度上 ifndef 一般慢于 program once，特别是在大型项目上， 区别会比较明显，所以越来越多的编译器开始支持 program once。
- ifndef 作用于某一段被包含（define 和 endif 之间）的代码， 而 program once 则是针对包含该语句的文件， 这也是为什么 program once 速度更快的原因。
- 如果用 ifndef 包含某一段宏定义，当这个宏名字出现“撞车”时，可能会出现这个宏在程序中提示宏未定义的情况（在编写大型程序时特性需要注意，因为有很多程序员在同时写代码）。
- program once 针对整个文件， 因此它不存在宏名字“撞车”的情况， 但是如果某个头文件被多次拷贝，program once 无法保证不被多次包含，因为program once 是从物理上判断是不是同一个头文件，而不是从内容上。

#### Q22 i++与++i

 理论上++i更快，实际与编译器优化有关。

```c++
//i++实现代码为：
int operator++(int){
    int temp = *this;
    ++*this;
    return temp;
}//返回一个int型的对象本身
// ++i实现代码为：
int& operator++(){
    *this += 1;
    return *this;
}//返回一个int型的对象引用
```

i++和++i的考点比较多，简单来说，就是i++返回的是i的值，而++i返回的是i+1的值。也就是++i是一个确定的值，是一个可修改的左值，如下使用： 

```c++
cout << ++(++(++i)) << endl;
cout << ++ ++i << endl;
```

 可以不停的嵌套++i 

```c++
int main(){
    int i = 1;
    printf("%d,%d\n", ++i, ++i);    //3,3
    printf("%d,%d\n", ++i, i++);    //5,3
    printf("%d,%d\n", i++, i++);    //6,5
    printf("%d,%d\n", i++, ++i);    //8,9
    system("pause");
    return 0;
}
```

**首先是函数的参数入栈顺序从右向左入栈的，计算顺序也是从右往左计算的，不过都是计算完以后再进行的压栈操作：**
对于第1个printf，首先执行++i，返回值是i，这时i的值是2，再次执行++i，返回值是i，得到i=3，将i压入栈中，此时i为3，也就是压入3，3；
对于第2个printf，首先执行i++，返回值是原来的i，也就是3，再执行++i，返回值是i，也就是5,依次将3，5压入栈中得到输出结果；
对于第3个printf，首先执行i++，返回值是5，再执行i++返回值是6，依次将5，6压入栈中得到输出结果；
对于第4个printf，首先执行++i，返回i，此时i为8，再执行i++，返回值是8，此时i为9，依次将i，8也就是9，8压入栈中，得到输出结果。
上面的分析基于VS2019，不过准确来说函数多个参数的计算顺序是未定义的(the order of evaluation of function arguments are undefined)。运行结果随不同的编译器而异。 

#### Q23 指针和引用

相同点：

- 都是地址的概念；
- 都是“指向”一块内存。指针指向一块内存，它的内容是所指内存的地址；而引用则是某块内存的别名；
- 引用在内部实现其实是借助指针来实现的，一些场合下引用可以替代指针，比如作为函数形参。

不同点：

-  指针是一个实体，而引用(看起来，这点很重要)仅是个别名；
- 引用只能在定义时被初始化一次，之后不可变；指针可变；引用“从一而终”，指针可以“见异思迁”；
- 引用不能为空，指针可以为空；
-  “sizeof 引用”得到的是所指向的变量(对象)的大小，而“sizeof 指针”得到的是指针本身的大小；
- 指针和引用的自增(++)运算意义不一样；
- 引用是类型安全的，而指针不是 (引用比指针多了类型检查)
- 引用具有更好的可读性和实用性。

**引用占用内存空间吗？**

如下代码中对引用取地址，其实是取的引用所对应的内存空间的地址。这个现象让人觉得引用好像并非一个实体。但是引用是占用内存空间的，而且其占用的内存和指针一样，因为引用的内部实现就是通过指针来完成的。

比如 Type& name； <===> Type\* const name。

```c++
int main(void){
    int a = 8;
    const int &b = a;
    int *p = &a;
    int* p =const_cast<int *>( &b);
    *p = 0;
    cout << b << endl;; //output 0
    cout<<a; //output 0
    return 0;
}
```

 **三目运算符**

在C中三目运算符(? :)的结果仅仅可以作为右值，比如如下的做法在C编译器下是会报错的，但是C++中却是可以是通过的。这个进步就是通过引用来实现的，因为下面的三目运算符的返回结果是一个引用，然后对引用进行赋值是允许的。

```c++
int main(void){
    int a = 8;
    int b = 6;
    (a>b ? a : b) = 88;
    cout<<a; //output 88
    return 0;
}
```

#### Q24 指针数组和数组指针

数组指针，是指向数组的指针，而指针数组则是指该数组的元素均为指针。

- 数组指针，是指向数组的指针，其本质为指针，形式如下。如 int (*p)[10]，p即为指向数组的指针，()优先级高，首先说明p是一个指针，指向一个整型的一维数组，这个一维数组的长度是n，也可以说是p的步长。也就是说执行p+1时，p要跨过n个整型数据的长度。数组指针是指向数组首元素的地址的指针，其本质为指针，可以看成是二级指针。

```c++
类型名 (*数组标识符)[数组长度]
```

- 指针数组，在C语言和C++中，数组元素全为指针的数组称为指针数组，其中一维指针数组的定义形式如下。指针数组中每一个元素均为指针，其本质为数组。如 int \*p[n]， []优先级高，先与p结合成为一个数组，再由int\*说明这是一个整型指针数组，它有n个指针类型的数组元素。这里执行p+1时，则p指向下一个数组元素，这样赋值是错误的：p=a；因为p是个不可知的表示，只存在p[0]、p[1]、p[2]…p[n-1],而且它们分别是指针变量可以用来存放变量地址。但可以这样\*p=a ; 这里*p表示指针数组第一个元素的值，a的首地址的值。

```c++
类型名 *数组标识符[数组长度]
```

#### Q25 数组名前加取地址符

```c++
int main(){
    int a[10]={1,2,3,4,5,6,7,8,9,10};
    cout<<a+1<<"\t"<<&a+1<<endl;
    cout << &a[1]+1 << endl;
	cout <<*( &a[1] + 1) << endl;
    return 0;
}
```

```c++
00FFF6C4        00FFF6E8
00FFF6C8
3

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 5560)已退出，代码为 0。
按任意键关闭此窗口. . .
```

我们知道数组名与数组名取地址所得到的地址都是一样的。由第二行输出可以知道a+1后指针指向的是组的第二个元素，而&a+1后指针指向的是整个数组内存空间的末尾。

- 由于数组和指针的关系，a相当于一个指针，指向的是第一个元素，也就是传说中数组的首地址。此时这个指针的步长是数组元素类型所占的字节数，在此为4Byte。
- &a这货代表的不是a这个变量的地址，而是数组元素的地址，它代表整个数组！！所以&a这个指针的步长是整个数组所占的字节数！！也就是说a的类型是int[10]，&a的类型是int(\*)[10]，是个指向int[10]数组的指针，&a[0]的类型是int\*。

#### Q26 程序内存分配情况

C、C++中内存分配方式可以分为三种：

- 从静态存储区域分配：
  内存在程序编译时就已经分配好，这块内存在程序的整个运行期间都存在。速度快、不容易出错，因为有系统会善后。例如全局变量，static 变量等。
  
-  在栈上分配：
  在执行函数时，函数内局部变量的存储单元都在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。
  
- 从堆上分配：
  即动态内存分配。程序在运行的时候用 malloc 或 new 申请任意大小的内存，程序员自己负责在何时用 free 或 delete 释放内存。动态内存的生存期由程序员决定，使用非常灵活。如果在堆上分配了空间，就有责任回收它，否则运行的程序会出现内存泄漏，另外频繁地分配和释放不同大小的堆空间将会产生堆内碎块。

一个 C、C++程序编译时内存分为 5 大存储区：堆区、栈区、全局区、文字常量区、程序代码区。 

#### Q27 strcpy,sprintf, memcpy

三者主要有以下不同之处：

- 操作对象不同，strcpy 的两个操作对象均为字符串，sprintf 的操作源对象可以是多种数据类型，目的操作对象是字符串，memcpy 的两个对象就是两个任意可操作的内存地址，并不限于何种数据类型。

- 执行效率不同，memcpy 最高，strcpy 次之，sprintf 的效率最低。

- 实现功能不同，strcpy 主要实现字符串变量间的拷贝，sprintf 主要实现其他数据类型格式到字符串的转化，memcpy 主要是内存块间的拷贝。

**说明：**strcpy、sprintf 与 memcpy 都可以实现拷贝的功能，但是针对的对象不同，根据实际需求，来选择合适的函数实现拷贝功能。 

#### Q28 输入逗号隔开的字符串

 输入格式为：**名字,年龄,学号,分数1,分数2,分数3,分数4** 其中**名字**和**学号**为字符串，其余均为非负整数。 

```c++
char name[5];//name指针必须已经分配了空间char* name=new char(5);
scanf("%s,%d,%s,%d,%d,%d,%d", name, &age, num, &s1, &s2, &s3, &s4);
```

运行时出现问题，经过排查发现name字符数组中包含所有的输入（包括逗号），而其他的变量没有被任何操作。后来仔细一想，原来「,」也属于字符，在输入时name字符串把「,」给吃了。于是想起`%[^]`控制符，表示的意思是输入任意多的字符，直到遇到指定的字符。

修改后满足了要求：

```c++
scanf("%[^,],%d,%[^,],%d,%d,%d,%d", name, &age, num, &s1, &s2, &s3, &s4);
```

#### Q29 asert()函数

assert宏的原型定义在assert.h中，其作用是如果它的条件返回错误，则终止程序执行.

```c++
 #include "assert.h"
void assert( int expression );
```

#### Q30 取消对NULL指针"X"的引用

C6011:取消对NULL指针"X"的引用

此警告表明正在取消引用空指针,如果指针的值无效,结果是未定义的 

```c++
#include<malloc.h>
void f(){
	char *p = (char*)malloc(10);
	*p = '\0';
	//balabala...
	free(p);
}
```

 这个代码会触发警告,因为如果没有足够的可用内存,对malloc 的调用可能返回NULL,下面的就对了 

```c++
#include<malloc.h>
void f(){
	char *p = (char*)malloc(10);
	if(p){
		*p='\0';
		//balabalabala......
		free(p);
	}
}
```

#### Q31  rand()

C 库函数 **int rand(void)** 返回一个范围在 0 到 *RAND_MAX* 之间的伪随机数。

RAND_MAX 是一个常量，它的默认值在不同的实现中会有所不同，但是值至少是 32767。

#### Q32  calloc

calloc = malloc+memset 但是推荐calloc

calloc分配内存，也初始化

malloc只分配内存，不初始化

memset只能用来初始化
在刷题中对数组初始化`int arr[10] = {0}//10`可能是个很大的值，提交不对要么利用循环进行初始化要么用calloc或者malloc+memset推荐使用calloc

calloc 在stdlib.h的头文件中calloc原型
`void *calloc(size_t nitems, size_t size)`

nitems -- 要被分配的元素个数

 size -- 元素的大小。

 // 用法 动态分配一个数组，数组有10个int型元素，每个都是0

`int *arr = (int *) calloc(10, sizeof(int))`
memset在string.h中void *memset(void *str, int c, size_t n)

str -- 指向要填充的内存块

c -- 要被设置的值

该值以 int 形式传递，但是函数在填充内存块时是使用该值的无符号字符形式。

n -- 要被设置为该值的字节数。
用法

`int *arr = new int[10];  // 动态`

或者

`int arr[10]; //静态分配数组`

```c++
// 将数组所有元素初始化为0
memset(arr,0,sizeof(int)*10);
int *arr2 = (int *)malloc(sizeof(int)*10);// 动态分配10个int的空间
memset(arr2, 0,sizeof(int)*10); // 给10个int初始化为0
```

#### Q33. C++有哪些性质

封装，继承和多态。（即面向对象特点）

#### Q34.多态，虚函数，纯虚函数

多态：是对于不同对象接收相同消息时产生不同的动作。

C++的多态性具体体现在运行和编译两个方面：

- 在程序运行时的多态性通过继承和虚函数来体现；

- 在程序编译时多态性体现在函数和运算符的重载上；

虚函数：在基类中冠以关键字 virtual 的成员函数。 它提供了一种接口界面。允许在派生类中对基类的虚函数重新定义。

纯虚函数的作用：在基类中为其派生类保留一个函数的名字，以便派生类根据需要对它进行定义。作为接口而存在 纯虚函数不具备函数的功能，一般不能直接被调用。

从基类继承来的纯虚函数，在派生类中仍是虚函数。如果一个类中至少有一个纯虚函数，那么这个类被称为抽象类（abstract class）。

抽象类中不仅包括纯虚函数，也可包括虚函数。**抽象类必须用作派生其他类的基类，而不能用于直接创建对象实例**。但仍可使用指向抽象类的指针支持运行时多态性。

#### Q35 统计整数的二进制中1的个数

```c++
int func(x) { 
    int countx = 0; 
    while(x) { 
        countx ++; 
        x = x&(x-1); 
    } 
    return countx; 
} 
```

```c++
//判断数中1的位数
int fun(long x){
    int _count = 0;
    while(x){
    	if(x % 10 == 1)
   			++_count;
    	x /= 10;
    }
    return _count;
}
int main(){
    cout << fun(123321) << endl;
    return 0;
}
```

#### Q36 大/小端模式

**大端模式**：是指数据的高位，保存在内存的低地址中，而数据的低位，保存在内存的高地址中，这样的存储模式类似于把数据当作字符串顺序处理。地址由小向大增加，而数据从高位往低位放；
**小端模式**：是指数据的高位保存在内存的高地址中，而数据的低位保存在内存的低地址中，这种存储模式将地址的高低和数据位权有效地结合起来，高地址部分权值高，低地址部分权值低，和我们的逻辑方法一致。 

 在网络编程中，规定使用网络字节序(即大端模式) 

windows一般存储为小端模式

```c++
//判断大小端的方法1：指针
int main(int argc, char **argv){
    int a = 0x12345678;
    char *p = NULL;
    p = (char *)&a;
    printf("*p = 0x%x\n", \*p);
    return 0;
}
//输出结果为*p = 0x78 则为小端模式。
//输出结果为*p = 0x12 则为大端模式。
//判断大小端的方法2：共用体
union s{
    int a;
    char b;
};
int main(int argc, char *argv[]){
    union s s1;
    s1.a = 0x12345678;
    printf(“s1.b=0x%x\n”, s1.b);
    return 0;
}
//输出结果为s1.b= 0x78 则为小端模式。
//输出结果为s1.b= 0x12 则为大端模式。 
```

#### Q37 引用

就是某个目标变量的“别名”(alias)，对应用的操作与对变量直接操作效果完全相同。

申明一个引用的时候，切记要对其进行初始化。

引用声明完毕后，相当于目标变量名有两个名称，即该目标原名称和引用名，不能再把该引用名作为其他变量名的别名。

声明一个引用，不是新定义了一个变量，它只表示该引用名是目标变量名的一个别名，它本身不是一种数据类型，因此引用本身不占存储单元，系统也不给引用分配存储单元。

不能建立数组的引用。

**常引用**　

如果既要利用引用提高程序的效率，又要保护传递给函数的数据不在函数中被改变，就应使用常引用。常引用声明方式：`const 类型标识符 &引用名=目标变量名；`

例1

```c++
int a ; 
const int &ra=a;
ra=1; //错误
a=1; //正确
```

例2

```c++
string foo( ); 
void bar(string & s);
//那么下面的表达式将是非法的
bar(foo( ));
bar("hello world");
```

原因在于foo( )和"hello world"串都会产生一个临时对象，==在C++中，临时对象都是const类型的==。因此上面的表达式就是试图将一个const类型的对象转换为非const类型，这是非法的。引用型参数应该在能被定义为const的情况下，尽量定义为const 。

#### Q38 二维数组分配空间

```c++
const int n=10;
const int m=5 ;
int** dp = new int* [n];
for (int i = 0; i < n; i++)
    dp[i] = new int[m];
//...
for (int i = 0; i < n; i++)
    delete[] dp[i];
delete[]dp;
```

```c++
int **a; 
const int n=10;
const int m=5 ;
a = (int**)malloc(sizeof(int*)*n);//为二维数组分配3行 
for (int i = 0; i < n; ++i)//为每列分配5个大小空间 
    a[i] = (int*)malloc(sizeof(int)*m); 
//...
for (int i = 0; i < n; ++i)
    for (int j = 0; j < m; ++j)
        //...
        //a[i][j]=0;
//...
//分配空间加初始化为0
a = (int**)calloc(n,sizeof(int*));
for (int i = 0; i < n; ++i)
    a[i] = (int*)calloc(m,sizeof(int)); 
//...
for (int i = 0; i < n; ++i)
    free(a[i]); 
free(a); 
```

#### Q39 求平方根

```c++
int sqrt(int x) {
    long i = 1;//int会溢出
    while (i * i < x) {
        i *= 2;
    }
    while (i * i > x) {
        i = i - 1;
    }
    return (int)i;
}
```

#### Q40  说一下static关键字的作用

见c++之static

#### Q41 说一下C++和C的区别

**设计思想上：**

C++是面向对象的语言，而C是面向过程的结构化编程语言

**语法上：**

C++具有封装、继承和多态三种特性

C++相比C，增加多许多类型安全的功能，比如强制类型转换、

C++支持范式编程，比如模板类、函数模板等

#### Q42 C/C++ 中指针和引用的区别

1.指针有自己的一块空间，而引用只是一个别名；

2.使用sizeof看一个指针的大小是4，而引用则是被引用对象的大小；

3.指针可以被初始化为NULL，而引用必须被初始化且必须是一个已有对象的引用；

4.作为参数传递时，指针需要被解引用才可以对对象进行操作，而直接对引用的修改都会改变引用所指向的对象；

5.可以有const指针，但是没有const引用；

6.指针在使用中可以指向其它对象，但是引用只能是一个对象的引用，不能被改变；

7.指针可以有多级指针（**p），而引用至于一级；

8.指针和引用使用++运算符的意义不一样；

9.如果返回动态内存分配的对象或者内存，必须使用指针，引用可能引起内存泄露。

#### Q43 判断一个数的奇偶

 判断一个数是不是二的倍数，判断该数二进制末位是不是0：

a % 2 == 0 或者a & 0x01 == 0。

#### Q44 指针与数组的区别

| 指针                                                         | 数组                                 |
| :----------------------------------------------------------- | ------------------------------------ |
| 保存数据的地址                                               | 保存数据                             |
| 间接访问数据，首先获得指针的内容，然后将其作为地址，从该地址中提取数据 | 直接访问数据，                       |
| 通常用于动态的数据结构                                       | 通常用于固定数目且数据类型相同的元素 |
| 通过Malloc分配内存，free释放内存                             | 隐式的分配和删除                     |
| 通常指向匿名数据，操作匿名函数                               | 自身即为数据名                       |

