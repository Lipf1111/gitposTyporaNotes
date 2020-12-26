### freopen函数详解

（该是FREquently OPEN的缩写形式，，意为经常打开的文件）通常在设计好算法和程序后，要在调试环境（例如VC等）中运行程序，输入测试数据，当能得到正确运行结果后，才将程序提交到oj中。但由于调试往往不能一次成功，每次运行时，都要重新输入一遍测试数据，对于有大量输入数据的题目，输入数据需要花费大量时间。使用freopen函数可以解决测试数据输入问题，避免重复输入，不失为一种简单而有效的解决方法。 

函数名：freopen 
声明：FILE *freopen( const char *path, const char *mode, FILE *stream ); 
所在文件： stdio.h 
参数说明： 
path: 文件名，用于存储输入输出的自定义文件名。 
mode: 文件打开的模式。和fopen中的模式（如r-只读, w-写）相同。 
stream: 一个文件，通常使用标准流文件。 
返回值：成功，则返回一个path所指定文件的指针；失败，返回NULL。（一般可以不使用它的返回值） 
功能：实现重定向，把预定义的标准流文件定向到由path指定的文件中。标准流文件具体是指stdin、stdout和stderr。其中stdin是标准输入流，默认为键盘；stdout是标准输出流，默认为屏幕；stderr是标准错误流，一般把屏幕设为默认。  

#### freopen 用法

函数原形 FILE *freopen(char *filename, char *type, FILE *stream);
第一个参数 filename 是文件名
第二个参数一般是 "r" 或 "w"， "r" 代表是从文件读入，"w"代表是写
入到文件
第三个参数一般是 stdin 代表文件读入， 和第二个参数 "r" 连用
stdout 代表写入到文件，和 第二个参数 "w" 连用

用法举例

freopen("a.txt","r",stdin ); 执行这条语句后， 程序中下面所有的
读入将从文件 "a.txt" 中读入
如：

```c++
#include <stdio.h>
#include <stdlib.h>
int main(){
    char ch;
    freopen("a.txt","r",stdin);
    while( ch= getchar()!= '/n' )
        putchar(ch);
    return 0 ;
}
```

对于这个程序， 那么在控制台下的读入都无效， 他只会从文件 
"a.txt"中读入. 运行这个程序前你得先建一个文件 a.txt ， 与你的代码
生成的 .exe 文件在同一文件夹中。大家可以试试看。

*如果再加一个语句， 程序变为*

```c++
#include <stdio.h>
#include <stdlib.h>
int main(){
    char ch;
    freopen("a.txt","r",stdin);
    freopen("b.txt","w",stdout);
    while( ch= getchar()!= '/n' )
        putchar(ch);
    return 0 ;
}
```

程序不会输出任何东西在控制台下， 而把所有输出输出到文件 "b.txt" 中
这个 b.txt 文件可以先不建， 程序会自动在与 .exe 文件相同目录下建立

另外还有两个问题

1. 如何判断文件是否打开了

可以直接 

```c++
if( freopen("a.txt","r",stdin)== NULL ) return false;
```

或   

```c++
if( freopen("b.txt","w",stdout)== NULL ) return false;
```


表示没有打开

2. 如何使流重新回到控制台上

如果你不想输入或输出到文件了，就加上一句

```c++
freopen("CON","r",stdin ); //对应输入
freopen("CON","w",stdout); //对应输出
```

注意的问题， 因为参数都是 c_字符串， 故不能把 c++ 里面的 string 类对象作为参数传进去

比如 string str= "a.txt";
你不能这样写 freopen( str, "r", stdin );
可以先把 string 类对像化成 c_字符串, 就用 c_str() 函数
上面的可以这样写 freopen( str.c_str(), "r", stdin );

最后只要使用fclose关闭输入文件和输出文件即可。

fclose(stdin);

fclose(stdout);

若要恢复句柄，可以重新打开标准控制台设备文件，只是这个设备文件的名字是与操作系统相关的。

DOS/Windows:

freopen("CON", "r", stdin);

Linux:

freopen("/dev/console", "r", stdin);

#### 注意：

其路径可以填写绝对路径或者箱规路径

相对路径时：文件在工程文件目录下面，Debug文件外面。

