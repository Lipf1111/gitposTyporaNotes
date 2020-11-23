# [C++函数返回局部变量](https://www.cnblogs.com/fanhaha/p/7117766.html)

## 函数不可返回的

- **<font color=red>函数不能返回指向栈内存的指针</font>**
- **<font color=red>函数不能返回局部变量的指针或引用</font>**

原因：返回值是拷贝值，局部变量的作用域为函数内部，函数执行结束，栈上的局部变量会销毁，内存释放。

## 可返回的局部变量

### 1. 返回局部变量本身    

```c++
int sum(int a, int b)
{
    int s=a+b;
    return s;
}
```

### 2. 常量

```c++
char* returnValue()  
{  
    char* str="HelloJacky";  
    return str;  
}  
```

 `char * str=“hello world”`字符串常量，不能`str[1]='a'`，常量不能修改。 这种语法是为了兼容c代码。`“hello world” `是`const char * `类型。然后将char * 指向存储的位置。使用C++编译器会有警告提示：

warning: **deprecated** conversion from string constant to 'char*' [-Wwrite-strings]|
推荐把类型修改为const char *
也就是说，这种语法的存在，只是为了保证以前的C代码可以正常编译。 

```c++
//错误
char* returnValue()  
{  
    char str[]="HelloJacky";  
    return str;  
}  
```

str 为局部变量

### 3. 静态局部变量

当多次调用一个函数且要求在调用之间保留某些变量的值时，可考虑采用静态局部变量。虽然用全局变量也可以达到上述目的，但全局变量有时会造成意外的副作用，因此仍以采用局部静态变量为宜。

```c++
//正确char* returnValue()  
{  
    static char str[]="HelloJacky";  
    return str;  
}  
```

强制定义为静态类型；

```c++
int* returnValue()  
{  
    static int value[3]={1,2,3};  
    return value;  
}  
```

数组名是不就变量的首地址，加上static 可以

### 4. 堆内存中的局部变量

```c++
//正确int *sum(int a, int b)
{
    int *s=new int();
    *s=a+b;
    return s;
}
```

```c++
char* newMemory(int size)  
{  
    char* p=NULL;  
    p=new char[size];  
    return p;  
}  

int _tmain(int argc, _TCHAR* argv[])  
{  
    char* p=newMemory(2);  
    if(p!=NULL)  
    {  
        *p='a';  
    }  
    std::cout<<*p;  
    delete [] p;  
    return 0;  
}  
```

在函数内new空间，在函数外delete空间。函数内申请空间，调用后释放空间。但这不是一种好的编程习惯，尽量在同一作用域内进行new和 delete操作。接口不灵活

 

五大内存分区(貌似与编译原理中不一样,不过道理是一样的,实际存在的东西总是会与理论有一定差距的)

1. 在C++中，内存分成5个区，他们分别是堆、栈、自由存储区、全局/静态存储区和常量存储区。
2. 栈，就是那些由编译器在需要的时候分配，在不需要的时候自动清楚的变量的存储区。里面的变量通常是局部变量、函数参数等。
3. 堆，就是那些由new分配的内存块，他们的释放编译器不去管，由我们的应用程序去控制，一般一个new就要对应一个delete。如果程序员没有释放掉，那么在程序结束后，操作系统会自动回收。
4. 自由存储区，就是那些由malloc等分配的内存块，他和堆是十分相似的，不过它是用free来结束自己的生命的。
5. 全局/静态存储区，全局变量和静态变量被分配到同一块内存中，在以前的C语言中，全局变量又分为初始化的和未初始化的，在C++里面没有这个区分了，他们共同占用同一块内存区。
6. 常量存储区，这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改（当然，你要通过非正当手段也可以修改，而且方法很多）