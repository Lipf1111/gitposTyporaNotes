### **标准I\O的缓冲类型**

#### 总结：

​	缓冲区的字符不主动清空不会清空，但是读取字符串时会跳过空白字符，而读取一个字符会接收缓冲区的存在字符（包括回车符、换行符）。对于cin>>的输入可以以空格、Tab,回车等分割。

　　标准I\O根据不同的应用需求，提供了全缓冲、行缓冲、无缓冲三种缓冲方式。

　　**全缓冲：**只有当划定的缓冲区被填满或者数据读取至末尾时，才开始执行 I\O 操作(执行系统提供的 read\write 操作)。磁盘文件的读写一般采用这种方式。

　　**行缓冲：**当输入输出过程遇到换行符''\n"或者当分配缓冲区已满时，才开始执行 I\O 操作。一般涉及终端的读写操作如 stdin 与 stdout 使用这种缓冲方式。

　　**无缓冲：**当有数据产生时，马上由相应的设备进行处理。一般来说 stderr(standard error) 使用这种缓冲方式，使得有错误信息时马上能够得到响应。**需要注意的是,标准库不缓存并不意味着操作系统或者设备驱动不缓存**。

> 注意，以上关于 stdin/stdout 的缓冲方式并不是直接规定死的。一些语言的语言规范会对缓冲实现给出一定的限制，但并不具体，只是许多标准I/O是以上述方式实现的而已

**行缓冲**

　　标准输入缓冲区 stdin 使用行缓冲的方式存储输入。用户的输入数据首先被暂存在临时缓冲区中，当用户键入回车键或临时缓冲区满后，stdin 才进行 I/O 操作，将数据由临时缓冲区拷贝至 stdin 中。C语言提供的输入输出函数如 scanf 、getchar 等则从上述缓冲区 stdin 中读取数据输入。

　　scanf 和 getchar 等函数会在 stdin 中读取数据，若上述缓冲区中已存在数据，则直接读取其中的数据，若上述缓冲区为空，则上述函数会挂起，等待数据缓冲的完成( 用户输入回车键或数据缓冲区满后， stdin 会进行数据缓冲，之后上述函数才能继续执行)。 **用户一次输入的数据可能会超过 scanf 、getchar 等函数调用所需要的数据，那么所需数据被读取后，剩余的数据仍会存放在缓冲区中，之后的函数调用会直接读取 stdin 中已有的数据。**只有当缓冲区为空后，scanf 等函数才会等待用户输入(实际应该是等待 stdin 的缓冲。

### **scanf函数**

　　scanf函数: scanf C++ reference

　　函数声明：int scanf( format string , arg1 , arg2 , ...)；

　　从函数声明可以看到，scanf 的参数由指示读取动作的格式化字符串( format string )和相应的地址参数 arg1...argn 组成。scanf 函数将输入从标准输入缓冲区 stdin 中读入，并将它们以格式化字符串中指定的格式存储到额外的参数 arg1...arg2 等指定的内存空间中。其中额外的参数(additional argument)指向的内存空间的数据类型应该与格式化字符串中指定的数据类型相一致。

**格式化字符串(format string)**　　

　　格式化字符串规定了 scanf 等函数如何从输入缓冲 stdin 中读取数据，其组成字符的含义如下所示：

1. 空白字符(whitespace)。**scanf 会读取并忽略在 stdin 中下一个非空白字符之前的所有空白字符(空格、换行和 tab)**，然后读取格式化字符串中规定格式的数据。若格式化字符串中包含空白字符，则该空白字符会与输入缓冲区中任意数量的连续空白字符相匹配，并将其从缓冲区中清除(包括0个)。例如格式化字符串"%d %d"，会要求 scanf 首先从缓冲区中读取一个整型(若之前存在空白字符则跳过)，再跳过输入缓冲区中连续的空白字符(与格式化字符串中的空白字符匹配)，最后再读取一个整形
2. 非空白字符(non whitespace)。对于格式化字符串中既非空白字符又不是格式说明符(format specifier,由%标识)的一部分的字符，scanf 会尝试从 stdin 中读取输入，并将输入与该字符比较，若匹配，则继续进行后续读取，若不匹配，则函数返回错误信息；
3. 格式说明符。以 % 开头的用于指定输入数据格式的字符。如 %d 指定需要读取一个整形，%s 需要读取一个字符串。scanf 等函数首先根据格式说明符尝试去解析 stdin 中的数据，如对于 %d ，scanf 会尝试对 stdin 中已有数据以整型的格式进行解析。若解析成功，则将上述解析结果存放到指定的内存中，若解析失败，如 stdin 中仅存在一个字符 'a'，scanf 会退出并返回，**但是上述不匹配的数据并不会从缓冲区中清除，后续的 scanf 调用仍从上述输入开始读取**；

由以上3条规则，**通过设置格式化字符串可以规定了 scanf 函数的行为**。下面为示例：

- scanf("%s,%d",&a,&b);　　//scanf需先读取一个字符串，再读取一个 '，'(规则2)，最后读取一个整数
- scanf("%d\t%d",&a,&b);　 //scanf需先读取一个整数，再将格式化字符串中的 '\t' (空白字符)与缓冲区中0个或多个空白字符匹配并清除(规则1)，最后读取一个整数
- scanf("%d%d",&a,&b);　　//scanf需要先读取一个整数，之后再读取一个整数，两个整数之间的空白字符会被忽略(规则1)

**字符和字符串的读取**

- 对于 stdin 中的字符的读取，scanf 、 getchar 等函数会读取缓冲区中的第一个字符，包括空白字符和非空白字符。
- 对于 stdin 中的字符串的读取，scanf 会在开始处理后(跳过第一个非空白字符之前的空白字符，规则1)读取到的第一个空白字符处退出，并在读取的字符串尾部加入'\0'作为结束标志。

**缓冲区读取数据问题示例**

> 例1

　　程序先输出变量未初始化之前的值，再使用scanf读取输入，再显示读取输入之后的值

```c++
#include <stdio.h>
int main() {
	int a=1, b=2;
	char c='a';
	printf("%d,%d,%c\n", a, b, c);   //输出未初始化之前的值
	scanf("%d%d", &a, &b);
	scanf("%c", &c);
	printf("%d,%d,%c\n", a, b, c);   //输出初始化之后的值
	return 0;

}
```

```text
1,2,a
12 a
12,2,a

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 15124)已退出，代码为 0。
按任意键关闭此窗口. . .
```

　解释如下：

1. 用户输入至缓冲区中的数据实际为 12 + 空格 + a + 换行符 
2. 第一次读取输入时，首先将读取到的第一个数字12赋值给变量 a，之后 scanf 会试图读取下一个十进制数，但是发现下一个非空白字符(忽略输入的空格)为字符 'a'，与其所需要读取的数据类型不符，scanf 会退出并返回一个常数值来表示错误信息.此时字符 'a' 并未被读取，仍然存在于缓冲区中
3. 第二次读取输入时，scanf 就会发现缓冲区中第一个非空白字符为字符 'a'，从而会将字符 'a' 赋值给变量 c，并退出。

　　故而，再次输出变量时，变量 a 和 c 均已改变，而变量 b 只能保持原值。

```c++
#include <stdio.h>
int main() {
	int a = 1, b = 2;
	char c = 'w';
	char d;
	char e;
	printf("%d,%d,%c\n", a, b, c);   
	scanf("%d%d", &a, &b);
	printf("%d,%d,%c\n", a, b, c);   
	scanf("%c", &d);
	printf("%c\n",d);
	scanf("%c", &e);
	printf("%d", e);
	return 0;
}
```

```out
1,2,w
12 a
12,2,w
a
10
D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 12340)已退出，代码为 0。
按任意键关闭此窗口. . .
```

```c++
#include <stdio.h>
int main() {
	int a = 1, b = 2;
	char c ;
	char d;
	char e;
	printf("%d,%d,%c\n", a, b);   //输出时C的值随机，重新生成之后就会改变
	scanf("%d%d", &a, &b);
	scanf("%c", &c);
	printf("%d,%d,%c\n", a, b, c);   
	scanf("%c", &d);
	printf("%c\n",d);
	scanf("%c", &e);
	printf("%d", e);
	return 0;
}
```

```c++
#include <stdio.h>
int main() {
	int a = 1, b = 2;
	char c ;
	char d;
	char e;
	printf("%d,%d\n", a, b);   
	scanf("%d%d", &a, &b);
	scanf("%c", &c);
	printf("%d,%d,%d\n", a, b,(int)c);   
	scanf("%c", &d);
	printf("%d\n",(int)d);
	scanf("%c", &e);
	printf("%d", e);
	return 0;
}
```

```out
1,2
12 w
12,2,119
10
a
97
D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 14204)已退出，代码为 0。
按任意键关闭此窗口. . .
```



> 例2：

用于测试的函数先读取一个字符串，再读取一个字符，并将结果输出 

```c++
#include <stdio.h>
int main() {
	char a[10];
	char b;
	scanf("%s", a);
	scanf("%c", &b);
	printf("%s,%d", a, (int)b);
	return 0;
}
```

输入：abcd[回车]

```text
abcd
abcd,10
D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 2340)已退出，代码为 0。
按任意键关闭此窗口. . .
```

解释如下：

1. 用户在输入时，实际进入缓冲区中的数据为字符串'"abcd" + 换行符
2. 第一次读取时，scanf 会读取一个字符串，并在遇到第一个空白字符处停止,这里为换行符，即读取的字符串为"abcd"，scanf 函数还会在该字符串尾部加入'\0'进行存储
3. 第二次读取时，scanf 会读取一个字符，进行字符读取时空白符也被视为有效输入字符，故而 scanf 会读取换行符，而换行符的ASCII值即为10；

> 例3：

 将读取输入的要求换一下，要求读取两个字符串 

```c++
#include <stdio.h>
int main() {
	char a[10];
	char b[10];
	scanf("%s", a);
	scanf("%s", &b);
	return 0;
}
```

输入：abcd[回车]

```text
abcd

```

　原因在于在读取第一个字符串后，缓冲区中剩余一个换行符，而根据规则1，在读取字符串之前会跳过所有的空白字符，之后scanf会发现此时缓冲区已经为空，从而需要再次等待用户输入。

　　事实上，对于上述情况，除非第二次读取的参数是可以读取空白字符的 %c，其他的参数均会使得 scanf 认为缓冲区已为空，从而进入等待用户输入的状态。

### scanf_s

scanf_s() 的功能虽然与scanf() 相同，但却比 scanf() 安全，因为 scanf_s() 是针对“ scanf()在读取字符串时不检查边界，可能会造成内存泄露”这个问题设计的。
scanf_s()用于读取字符串时，必须提供一个数字以表明最多读取多少位字符，以防止溢出。 

```c++
 scanf_s("%c" , &v1,1); 
 scanf_s("%c,%s" , &v1,1,&v2,10); 
```

此时有必要限制为1，若为%s,也可以限制其他数字，但若是%d可以省略

### **getchar**

　　getchar 是用于字符输入的C库函数，其函数的声明包含在头文件 stdio.h，函数声明为： int getchar(void).其功能是**读取标准输入stdin中的一个字符**。

　　getchar 从标准输入中读取数据，而 stdin 是采用行缓冲的方式记录用户输入，也就是只有当用户键入回车键或输入至缓冲区末尾时，才会开始 I\O 操作，亦即读取一个字符。这样用户可以一次输入不止一个字符，读取过后缓冲区可能不为空。当再次调用 getchar 时，**若缓冲区不为空，getchar 就会直接读取在缓冲区中字符，而不是等待用户输入**。可以认为是getchar 等待的是行缓冲的完成，而不是用户输入的完成，在行缓冲完成后，**只要缓冲区不为空，getchar 就可以读取字符，而不需要等待用户输入**。

```c++
#include <stdio.h>
int main(void){
    char ch = '\0';
    while(ch != '\n'){
        printf("输入一个字符：");
        ch = getchar();
        printf("\n");
        putchar(ch);
        printf(":%d", (int)ch);
        printf("\n");
    } 
    return 0;
}
```

输入：abc[回车]

```text
输入一个字符：abc

a:97
输入一个字符：
b:98
输入一个字符：
c:99
输入一个字符：

:10

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 8820)已退出，代码为 0。
按任意键关闭此窗口. . .
```

可以明显看到，后续执行中并不要求用户输入，getchar()会直接读取缓冲区中的数据。而且**对于字符的读取操作而言，换行符'\n'也被视为一个字符**，而不是单纯的结束标志。 

**等待用户输入的字符输入**

　　getchar 可以直接从缓冲区中读取字符，而不等待用户输入，但这种方式也有可能带来潜在的错误。这里给出两种等待用户输入的字符传入方式（额外，可不看，免得混淆）。

1. 使用 getche 与 getch 函数。上述函数均从键盘上读入一个字节，其中后者不会将字符回显到屏幕上。以这两个函数读取字符时，都是通过调用函数读取一个键盘输入且只有一个。如调用 getche，键盘敲击 'abc' 时，只有一个字符 'a' 会被读取。其他字符为无效输入。但上述函数并不被包含在标准 C 函数库中，需要通过头文件 conio.h 来使用，并不被所有的编译器实现支持。
2. 在每次调用 getchar 函数之后，手动对缓冲区进行清除操作。可以使用 fflush() 函数清理缓冲区。C标准规定 fflush()函数可用来刷新输出（stdout）缓冲区(一般是将缓冲区数据写回存储设备)。但对于标准输入（stdin）则没有明确定义。部分编译器定义了 fflush( stdin )的实现，如微软的VC。也就是不同的编译器对于 fflush( stdin )的支持可能不同。GCC编译器没有定义它的实现，所以不能使用 fflush( stdin )来刷新输入缓冲区。

### cin简介

​	**cin**是C++编程语言中的标准输入流对象，即istream类的对象。cin主要用于从标准输入读取数据，这里的标准输入，指的是终端的**键盘**。

​	**cout**是流的对象，即ostream类的对象

​	**cerr**是标准错误输出流的对象，也是ostream 类的对象。

​	标准输出指的是终端**键盘**，标准错误输出指的是终端的**屏幕**。

​	在理解cin功能时，不得不提标准输入缓冲区。当我们从键盘输入字符串的时候需要敲一下回车键才能够将这个字符串送入到缓冲区中，那么敲入的这个回车键(\r)会被转换为一个换行符\n，这个换行符\n也会被存储在cin的缓冲区中并且被当成一个字符来计算！比如我们在键盘上敲下了123456这个字符串，然后敲一下回车键（\r）将这个字符串送入了缓冲区中，那么此时缓冲区中的字节个数是7 ，而不是6。

​	cin读取数据也是从缓冲区中获取数据，缓冲区为空时，cin的成员函数会阻塞等待数据的到来，一旦缓冲区中有数据，就触发cin的成员函数去读取数据。

#### cin常用的读取方法

​	使用cin从标准输入读取数据时，通常用到的方法有**cin>>，cin.get，cin.getline** 

##### cin>>的用法

```c++
#include <iostream>
using namespace std;
int main()
{
	char a;
	int b;
	float c;
	cin >> a >> b >> c;
	cout << a << " " << b << " " << c << " " << endl;
	system("pause");
	return 0;
}
```

 在屏幕中一次输入：a[回车]11[回车]5.56[回车]

```text
a
11
5.56
a 11 5.56
请按任意键继续...
```

输入：123[回车]1234

```text
123
1234
1 23 1234
请按任意键继续. . .
```

> 注意：

1. cin>>等价于cin.operator>>()，即调用成员函数operator>>()进行读取数据。
2. 当cin>>从缓冲区中读取数据时，若缓冲区中第一个字符是空格、tab或换行这些**分隔符**时，cin>>会将其忽略并**清除**，继续读取下一个字符，若缓冲区为空，则继续等待。但是如果读取成功，字符后面的分隔符是残留在缓冲区的，cin>>不做处理。
3. 不想略过空白字符，那就使用 noskipws 流控制。比如cin>>noskipws>>input; 

```c++
#include <string> 
#include <iostream>
using namespace std;
int main()
{
    char a;
    int b;
    float c;
    string str;
    cin>>a>>b>>c>>str;//cin>>(a)>>(b)>>(c)>>(str);
    cout<<a<<" "<<b<<" "<<c<<" "<<str<<endl;

    string test;
    getline(cin,test);//不阻塞
    cout<<"test:"<<test<<endl;
    system("pause");
    return 0;

}
```

从键盘输入: [回车] [回车] [回车]a[回车]5[回车]2.33[回车]hello[回车]，输出结果是： 

```text



a
5
2.33
hello
a 5 2.33 hello
test:
请按任意键继续...
```

​	从结果可以看出，cin>>对缓冲区中的第一个换行符视而不见，采取的措施是忽略清除，继续阻塞等待缓冲区有效数据的到来。但是，getline()读取数据时，并非像cin>>那样忽略第一个换行符，getline()发现cin的缓冲区中有一个残留的换行符，不阻塞请求键盘输入，直接读取，送入目标字符串后，再将换行符替换为空字符’\0’，因此程序中的test为空串。 

##### cin.get的用法

​	该函数有有多种重载形式，分为四种格式：无参，一参数，二参数，三个参数。常用的的函数原型如下： 

```c++
int cin.get();
istream& cin.get(char& var);
istream& get ( char* s, streamsize n );
istream& get ( char* s,  streamsize  n, char delim )。
```

 其中streamsize 在VC++中被定义为long long型。另外，还有两个重载形式不怎么使用，就不详述了，函数原型如下： 

```c++
istream& get ( streambuf& sb);
istream& get ( streambuf& sb, char delim );
```

###### cin.get读取一个字符

取一个字符(回车符等符号也能读取），可以使用cin.get或者cin.get(var)，示例代码如下： 

```c++
#include <iostream>
using namespace std;
int main()
{
    char a;
    char b;
    a=cin.get();
    cin.get(b);
    cout<<a<<b<<endl;
    system("pause");
    return 0;
}
```

输入：a,b,c,d[回车]

```Text
a,b,c,d
a,
请按任意键继续...
```

输入：e[回车]

```text
e
e

请按任意键继续...
```

> 注意：

- 从结果可以看出，cin.get()从输入缓冲区读取单个字符时不忽略分隔符，直接将其读取，就出现了如上情况，将换行符读入变量b，输出时打印两次(一个输入时的回车一个endl)。

- cin.get()的返回值是int类型，成功：读取字符的ASCII码值，遇到文件结束符时，返回EOF，即-1，Windows下标准输入输入文件结束符为Ctrl+z，Linux为Ctrl+d。cin.get(char var)如果成功返回的是cin对象，因此可以支持链式操作，如cin.get(b).get(c)。

###### cin.get读取一行

​	读取一行可以使用istream& get ( char* s, streamsize n )或者istream& get ( char* s, size_t n, streamsize delim )。二者的区别是前者默认以换行符结束，后者可指定结束符。n表示目标空间的大小。示例代码如下： 

```c++
#include <iostream>
using namespace std;
int main()
{
	char a;
	char array[20] = { NULL };
	cin.get(array, 20);
	cin.get(a);
	cout << array << " " << (int)a << endl;
	system("pause");
	return 0;
}
```

 输入：123456789[回车]

```text
123456789
123456789 10
请按任意键继续...
```

> 注意：

- 从结果可以看出，cin.get(array,20);读取一行时，遇到换行符时结束读取，但是不对换行符进行处理，换行符仍然残留在输入缓冲区。第二次由cin.get()将换行符读入变量b，打印输入换行符的ASCII码值为10。这也是cin.get()读取一行与使用getline读取一行的区别所在。getline读取一行字符时，默认遇到’\n’时终止，并且将’\n’直接从输入缓冲区中删除掉，不会影响下面的输入处理。

- cin.get(str,size);读取一行时，只能将字符串读入C风格的字符串中，即char*，但是C++的getline函数可以将字符串读入C++风格的字符串中，即string类型。鉴于getline较cin.get()的这两种优点，建议使用getline进行行的读取。关于getline的用法，下文将进行详述。

###### cin.getline读取一行

函数作用：从标准输入设备键盘读取一串字符串，并以指定的结束符结束。
函数原型有两个：

```c++
istream& getline(char* s, streamsize count); //默认以换行符结束
istream& getline(char* s, streamsize count, char delim);
```

```C++
#include <iostream>
using namespace std;
int main()
{
	char a;
	char array[20] = { NULL };
	cin.getline(array, 20,'a'); //或者指定结束符a，使用下面一行
	//cin.getline(array,20,'\n');
	cin.get(a);
	cout << array << ":"<<a<<endl;
	system("pause");
	return 0;
}
```

输入：123456789a[回车]

```text
12356789a
12356789:

请按任意键继续. . .
```

>  注意

cin.getline与cin.get的区别是，cin.getline不会将结束符或者换行符残留在输入缓冲区中 

###### cin的条件状态

使用cin读取键盘输入时，难免发生错误，一旦出错，cin将设置条件状态(condition state)。条件状态标识符号为:
goodbit:无错误
eofbit:已到达文件尾
failbit:非致命的输入/输出错误，可挽回
badbit:致命的输入/输出错误,无法挽回
若在输入输出类里.需要加[iOS](http://lib.csdn.net/base/ios)::标识符号。与这些条件状态对应的就是设置、读取和判断条件状态的流对象的成员函数。他们主要有：
s.eof()：若流s的eofbit置位，则返回true；
s.fail()：若流s的failbit置位，则返回true；
s.bad()：若流s的badbit置位，则返回true；
s.good()：若流s的goodbit置位，则返回true；
s.clear(flags)：清空状态标志位，并将给定的标志位flags置为1，返回void。
s.setstate(flags)：根据给定的flags条件状态标志位，将流s中对应的条件状态位置为1，返回void。
s.rdstate()：返回流s的当前条件状态，返回值类型为strm::iostate。strm::iostate 机器相关的整形名,由各个iostream类定义,用于定义条件状态。

了解以上关于输入流的条件状态与相关操作函数，下面看一个因输入缓冲区未读取完造成的条件状态位failbit被置位，再通过clear()复位的例子。

```c++
#include <iostream>
using namespace std;
int main()
{
	char ch, str[20]; 
    cin.getline(str, 5);
    cout<<"flag1:"<<cin.good()<<endl;    // 查看goodbit状态，即是否有异常
    cin.clear();                         // 清除错误标志
    cout<<"flag1:"<<cin.good()<<endl;    // 清除标志后再查看异常状态
    cin>>ch; 
    cout<<"str:"<<str<<endl;
    cout<<"ch :"<<ch<<endl;

    system("pause");
    return 0;

}
```

 输入：12345[回车] 

```text
12345
flag1:0
flag1:1
str:1234
ch :5
请按任意键继续. . .
```

输入：123[回车]

打印

```text
123
flag1:1
flag1:1
```

再输入：1

```text
1
str:123
ch :1
请按任意键继续. . .
```

输入：23456789[回车]

```text
23456789
flag1:0
flag1:1
str:2345
ch :6
请按任意键继续. . .
```

可以看出，因输入缓冲区未读取完造成输入异常，通过clear()可以清除输入流对象cin的异常状态。不影响后面的cin>>ch从输入缓冲区读取数据。因为cin.getline读取之后，输入缓冲区中残留的字符串是：5[换行]，所以cin>>ch将5读取并存入ch，打印输入并输出5。

如果将clear()注释，cin>>ch;将读取失败，ch为空。
cin.clear() 等同于cin.clear(ios::goodbit);因为cin.clear()的默认参数是ios::goodbit，所以不需显示传递，故而你最常看到的就是:
cin.clear()。

###### cin清空输入缓冲区

从上文中可以看出，上一次的输入操作很有可能是输入缓冲区中残留数据，影响下一次的输入。那么如何解决这个问题呢？自然而然，我们想到了在进行输入时，对输入缓冲区进行清空和状态条件的复位。条件状态的复位使用clear()，清空输入缓冲区应该使用：
函数原型：

```c++
istream &ignore( streamsize num=1, int delim=EOF );
```

函数作用：跳过输入流中n个字符，或在遇到指定的终止字符时提前结束（此时跳过包括终止字符在内的若干字符）。
使用示例如下：

```c++
#include <iostream>
using namespace std;
int main()
{
    char str1[20]={NULL},str2[20]={NULL};
    cin.getline(str1,5);
    cin.clear();  // 清除错误标志   
    cin.ignore(numeric_limitsstd::streamsize::max(),'\n'); //清除缓冲区的当前行
    cin.getline(str2,20);
    cout<<"str1:"<<str1<<endl;
    cout<<"str2:"<<str2<<endl;
    system("pause");
    return 0;
}
```

 程序输入：12345[回车]success[回车] 

```text
12345
success
str1:1234
str2:success
请按任意键继续. . .
```

> 注意： 

- 程序中使用cin.ignore清空了输入缓冲区的当前行，使上次的输入残留下的数据没有影响到下一次的输入，这就是ignore()函数的主要作用。其中，numeric_limits::max()不过是头文件定义的流使用的最大值，你也可以用一个足够大的整数代替它。
  如果想清空输入缓冲区，去掉换行符，使用：
  cin.ignore(numeric_limits< std::streamsize>::max()); 清除cin里所有内容。

- cin.ignore()；当输入缓冲区没有数据时，也会阻塞等待数据的到来。

- 有个疑问，网上很多资料说调用cin.sync()即可清空输入缓冲区，本人测试了一下，VC++可以，但是在linux下使用GNU C++却不行，无奈之下，linux下就选择了cin.ignore()。

#### 其它从标准输入读取一行字符串的方法

#### getline读取一行

C++中定义了一个在std名字空间的全局函数getline，因为这个getline函数的参数使用了string字符串，所以声明在了< string>头文件中了。

getline利用cin可以从标准输入设备键盘读取一行，当遇到如下三种情况会结束读操作：1）到文件结束，2）遇到函数的定界符，3）输入达到最大限度。

函数原型有两个重载形式：

```c++
istream& getline ( istream& is, string& str);//默认以换行符结束
istream& getline ( istream& is, string& str, char delim);
```

```c++
#include <string> 
#include <iostream>
using namespace std;

int main()
{
    string str;
    getline(cin,str);
    cout<<str<<endl;
    system("pause");
    return 0;
}
```

 输入：hello world[回车] 

```text
hello world
hello world
请按任意键继续...
```

> 注意

getline遇到结束符时，会将结束符一并读入指定的string中，再将结束符替换为空字符。因此，进行从键盘读取一行字符时，建议使用getline，较为安全。但是，最好还是要进行标准输入的安全检查，提高程序容错能力。

cin.getline()类似，但是cin.getline()属于istream流，而getline()属于string流，是不一样的两个函数。

#### gets读取一行

​	gets是C中的库函数，在< stdio.h>申明，从标准输入设备读字符串，可以无限读取，不会判断上限，以回车结束或者EOF时停止读取，所以程序员应该确保buffer的空间足够大，以便在执行读操作时不发生溢出。
函数原型：`char *gets( char *buffer );` 

```c++
#include <iostream>
using namespace std;
int main()
{
    char array[20]={NULL};
    gets(array);
    cout<<array<<endl;
    system("pause");
    return 0;
}
```

 输入：I am lvlv[回车] 

```text
I am lvlv
I am lvlv
请按任意键继续...
```

由于该函数是C的库函数，所以不建议使用，既然是C++程序，就尽量使用C++的库函数吧。 