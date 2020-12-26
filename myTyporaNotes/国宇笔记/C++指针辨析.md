### 指针辨析：悬垂指针、哑指针、野指针

### 野指针：

是指向“垃圾”内存（不可用内存）的指针
产生原因：

1. 指针创建时未初始化。指针变量刚被创建时不会自动成为NULL指针，它会随机指向一个内存地址。

指针申请之后要进行初始化，或者申请后直接为NULL;

野指针的产生是由于在首次使用之前没有进行必要的初始化。因此，严格地说，在编程语言中的所有未初始化的指针都是野指针。 

#### 野指针危害

1. 指向不可访问的地址
	危害：触发段错误。
2. 指向一个可用的，但是没有明确意义的空间
	危害：程序可以正确运行，但通常这种情况下，我们就会认为我们的程序是正确的没有问题的，然而事实上就是有问题存在，所以这样就掩盖了我们程序上的错误。
3. 指向一个可用的，而且正在被使用的空间
	危害：如果我们对这样一个指针进行解引用，对其所指向的空间内容进行了修改，但是实际上这块空间正在被使用，那么这个时候变量的内容突然被改变，当然就会对程序的运行产生影响，因为我们所使用的变量已经不是我们所想要使用的那个值了。通常这样的程序都会崩溃，或者数据被损坏。 

```c++
int f(int i){
    char *dp;    /* dp is a wild pointer */
    static char *scp;  /* scp is not a wild pointer:
                        * static variables are initialized to 0
                        * at start and retain their values from
                        * the last call afterwards.
                        * Using this feature may be considered bad
                        * style if not commented */
}
```



##### 2：指针操作超越了变量的作用范围。这种情况让人防不胜防，示例程序如下：

```c++
class A{
public:
	void Func(void){
		cout << "Func of class A" << endl;
	}
};
class B{
public:
	A * p;
	void Test(void){
		A a;
		p = &a;		// 注意 a 的生命期 ，只在这个函数Test中，而不是整个class B
	} 
	void Test1(void){
		p->Func();	// p 是“野指针”
	}
};
```

注意：函数 Test1 在执行语句 p->Func()时，对象 a 已经消失，而 p 是指向 a 的，所以 p 就成了“野指针”。 

##### 避免处理：

```c++
/* Alternative version for 'free()' */
void safefree(void **pp){
    if (pp != NULL) {      /* safety check */
        free(*pp);         /* deallocate chunk, note that free(NULL) is valid */
        *pp = NULL;        /* reset original pointer */
    }
}
int f(int i){
    char *p = NULL, *p2;
    p = (char *)malloc(1000);    /* get a chunk */
    p2 = p;      /* copy the pointer */
    /* use the chunk here */
    safefree(&p);/* safety freeing; does not affect p2 variable */
    safefree(&p);/* this second call won't fail */
    char c = *p2;/* p2 is still a dangling pointer, so this is undefined behavior. */
}
```



### **悬垂指针：**

1. 首先它不是NULL指针，其次他指向的内存是不合法的，这个不合法的内存俗称“垃圾”内存。它产生的原因一个是在free或是delete后，没有及时将指针设置为NULL。**危害**：若该指针还在使用，则可能（其他程序正好使用该空间）再次读取该位置数据，而发生程序错误，甚至系统问题。
2. 当多个指针指向一块内存时，对其中的一个指针施加了free或是delete后，即使把这个指针设置为了NULL，可是其余的指向这块内存的指针也是指向了不合法内存的。取名为悬垂指针，悬垂引用也是这个道理。 

解决策略：

1. 指针delete之后要进行设置为NULL.
2. 引入智能指针可以防止垂悬指针出现。一般是把指针封装到一个称之为智能指针类中，这个类中另外还封装了一个使用计数器，对指针的复制等操作将导致该计数器的值加1，对指针的delete操作则会减1，值为0时，指针为NULL。



指针p被free或者delete之后，没有置为NULL，让人误以为p是个合法的指针。别看free和delete的名字（尤其是delete），它们只是把指针所指的内存给释放掉，但并没有把指针本身干掉。通常会用语句if (p != NULL)进行防错处理。很遗憾，此时if语句起不到防错作用，因为即便p不是NULL指针，它也不指向合法的内存块。例 

```c++
#include "bits_stdc++.h"
using namespace std;
int main(void){
	char* p = (char*)malloc(100);
	strcpy(p, "hello");//#1
	free(p);	// p 所指的内存被释放，但是p所指的地址仍然不变，原来的内存变为“垃圾”内存（不可用内存 
	if (p != NULL){	// 没有起到防错作用 
		strcpy(p, "world");
	}
	for (int i = 0; i < 5; i++) //i=5后为乱码 
		cout << *(p + i) << " ";
	cout << endl;
}
/*
三个警告：
C6387	“p”可能是“0”: 这不符合函数“strcpy”的规范。
C6001	使用未初始化的内存“p”。
C6011	取消对 NULL 指针“p+i”的引用。查看第 #1 以找出可能会发生此情况的前一位置
*/
```

```out
w o r l d

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 3132)已退出，代码为 0。
按任意键关闭此窗口. . .
```

 另外一个要注意的问题：不要返回指向栈内存的指针或引用，因为栈内存在函数结束时会被释放。

```cpp
int *p=NULL;
void main(){
    int i=10;p=&i;
    cout<<"第一次：*p = "<<*p<<endl;
    cout<<"第二次：*p = "<<*p<<endl;
}
```

```out
第一次：*p = 10
第二次：*p = 10

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 15136)已退出，代码为 0。
按任意键关闭此窗口. . .
```

另：

```cpp
int *p=NULL;
void fun(){
    int i=10;
    p=&i;
}
void main(){
    fun();
    cout<<"第一次：*p = "<<*p<<endl;//函数返回值的生命周期到这一行结束？？？
    cout<<"第二次：*p = "<<*p<<endl;
}
```


输出结果为：

```out
第一次：*p = 10
第二次：*p = 2039794048

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 11944)已退出，代码为 0。
按任意键关闭此窗口. . .
```

得出结论：第二段程序中，由于fun()函数中的临时变量被销毁，故第二次输出时，==\*p已经成为悬垂指针==。

```c++
int* p ;
void fun() {
	int i = 10;
	p = &i;
    cout << "第0次：p = " << p << endl;//
	cout << "第0次：*p = " << *p << endl;//
}
void main() {
	fun();
	cout << "第一次：p = " << p << endl;
	cout << "第一次：*p = " << *p << endl;//
	cout << "第二次：p = " << p << endl;
	cout << "第二次：*p = " << *p << endl;
}
```

```out
第0次：p = 00CEFE00
第0次：*p = 10
第一次：p = 00CEFE00
第一次：*p = 2080950656
第二次：p = 00CEFE00
第二次：*p = 2080950656

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 11984)已退出，代码为 0。
按任意键关闭此窗口. . .
```



#### 哑指针

哑指针指传统的C/C++指针，它只是一个指向，除此以外它不会有其他任何动作，所有的细节必须程序员来处理，比如指针初始化,释放等等



#### 如何避免指针问题

1. 定义一个指针变量时一定记得初始化 
2. 动态开辟的内存空间使用完free之后一定将对应的指针置为NULL
3. 不要在函数中返回栈空间的指针和引用
4. 注意在使用时对指针的合法性的判断 

```c++
const string &manip(const string&); 
void main()  {
    string input; 
    cin >> input;
    cout << "Your input is: " << manip(input)<< endl; 
} 
const string &manip(const string& s){
    string ret = s;
    return ret;
}
//会出现错误，返回值为栈中引用（指针也不行）
//可以为：
const string manip(const string& s){
    string ret = s;
    return ret;
}
```

```c++
Person* GetInt(){
   Person *p = new Person();
   p->name = "wjei";
   p->age = 100;
   return p;
}  //正确
```

```c++
char* strCpy(char* str1,const char* str2){
	assert((str2 != NULL) && (str1 != NULL));
	char* address = str1;
	while((*str1++ = *str2++) != '\0');
	return address;  
} //正确
//返回了address 是局部变量，但是address指向的内存还没有释放，address所指向的内存和str1指向内存相同，
//在调用函数作用域范围内还存在，所以可以返回。不会出现垂悬指针的问题
```

函数可以返回一个局部对象，而不能返回一个局部对象的引用（指针）：
当函数返回一个局部对象时，虽然这个对象已经释放，但是返回时会产生一个临时对象。
而当返回一个局部对象的引用时，这个对象已经不存在了。这就要求在函数参数中，包含一个引用或指针。

也就是说指针或者引用指向的空间已经不存在了，所以出错，但是若空间还存在则正确，如上一程序。

```c++
int &func(int a,int b,int &retsult){
    retsult = a + b;
    return &retsult
}//正确
```

但是如下代码是错误的（返回局部对象的引用）

```c++
int &func(int a,int b){
    int &j = a + b;
    return j;
} 
```


