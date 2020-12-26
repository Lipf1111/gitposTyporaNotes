### STL之iota使用

1. 在c语言编程中，经常会用到`atoi()`函数，该函数的功能是将字符串转换成整数(int)；如下： 

```c++
#include <stdlib.h> 
int atoi(const char *str);
/*
功能：atoi()会扫描str字符串，跳过前面的空格字符，直到遇到数字或正负号才开始做转换，而遇到非数字或字符串结束符('\0')才结束转换，并将结果返回返回值。
参数：
	str：待转换的字符串
【返回值】返回转换后的整型数；如果 str 不能转换成 int 或者 str 为空字符串，那么将返回 0
示例：
*/
void func(){
    char str1[] = "-10";
    int num1 = atoi(str1);
    printf("num1 = %d\n", num1);
}
```

结果： 

```
num1 = -10
```

而 char str1[] = "abc-1100def";结果是： num1 = 0

2. C++中iota是用来批量递增赋值vector的元素；这两个函数名字很相似，容易混淆，注意区分；一个在用在c中，一个是用在c++的`STL`中； 

```c++
#include<iostream>
#include<numeric>   	//iota头文件
using namespace std;
void func(){
    vector<int> v(10);
    iota(v.begin(),v.end(),1);
    vector<int>::iterator it = v.begin();
    while(it != v.end()){
        cout<<*it++<<" ";
    }
}
```

结果：

```
1	2	3	4	5	6	7	8	9	10
```

3. itoa函数

 以下是用itoa()函数将整数转换为字符串的一个例子：

```c++
# include <stdio.h>
# include <stdlib.h>
void main (void){
    int num = 100;
    char str[25];
    itoa(num, str, 10);
    printf("The number 'num' is %d and the string 'str' is %s. \n" ,
    num, str);
}
```

itoa()函数有3个参数：第一个参数是要转换的数字，第二个参数是要写入转换结果的目标字符串，第三个参数是转移数字时所用 的基数。在上例中，转换基数为10。10：十进制；2：二进制...

itoa并不是一个标准的C函数，它是Windows特有的，如果要写跨平台的程序，请用sprintf。是Windows平台下扩展的，标准库中有sprintf，功能比这个更强，用法跟printf类似：

```c++
char str[255];
sprintf(str, "%x", 100); //将100转为16进制表示的字符串。 
```

