### char\*与char[]的区别

char\* ptr1=”abc”;ptr1是一个指针，ptr1所指向的地址的内容是不可改变的。ptr1是指向字符串常量(不可被修改)的。  

```c++
#include <iostream>
using namespace std;
int main(){
	const char* ptr1 = "abcd";
	char* ptr2 = const_cast<char*>(ptr1);
	//ptr2[2] = 'z';//错误，但是没有报错，也无警告，运行明显变慢，且输出没有显示任何字符
	char ptr3[] = "1234";
	ptr2 = ptr3;
    cout << ptr2 << endl;
	return 0;
}
//输出：1234
//但是不能ptr2="1234";
//虽不能修改字符串，但可以使用其值，如char a=ptr2[2];
```

​	char ptr1[] =”cdef”;是一个数组，ptr1指向第一个元素所在的位置，一经分配就不能更改。 它的空间是则栈里分配的，可以被重新修改，但是ptr1不能够再指向其他空间。 

```c++
#include <iostream>
using namespace std;
int main(){
	char ptr1[] = "efgh";
    ptr1[0]='A';
	//ptr1 = "xyz";        //error C3863: 不可指定数组类型“char [5]
    					   //E0137	表达式必须是可修改的左值
	cout << ptr1 << endl;  // ptr1中的元素是可以被修改的
	return 0;
}
//输出:Afgh
```

```c++
#include <iostream>
using namespace std;
int main(){
	char ptr1[] = "efgh";
	char* ptr2 = ptr1;//相当于ptr2指向了栈中的数据"efgh"
	ptr2[0] = 'A';   //可以改变元素值
	cout << ptr1 << endl;
	cout << ptr2;
	return 0;
}//输出:Afgh
	 //Afgh
```

**原因**：

- char * 实际上定义了指向字符串的指针，由于指向的内容由编译器分配在==文字常量区==（存放常量字符串，程序结束后由系统释放）中，无法改变其值。

- char [] 分配在==栈==中（由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈），其值可改变。

```c++
#include <iostream>
using namespace std;
int main(){
	char ptr1[] = "efgh";
	char* ptr2 = ptr1;
	cout << &ptr1 << endl;
	cout << &ptr2 << endl;
	ptr1[0] = 'A';
	ptr2[0] = 'B';   
	cout << ptr1 << endl;
	cout << ptr2 << endl;;
	cout << &ptr2 << endl;
	return 0;
}
```

```latex
00D3F960
00D3F954//不知ptr1与ptr2的地址为何不同
Bfgh
Bfgh
00D3F954
```

```c++
int main(){
	char tmp[] = "123";
	char* p=tmp;
	cout << sizeof(tmp) << endl << sizeof(p) << endl;
	cout << p[0] << endl << p[1] << endl << p[2] << endl <<int( p[3]) << endl;
	return 0;
}
```

```out
4
4
1
2
3
0

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 11408)已退出，代码为 0。
按任意键关闭此窗口. . .
```

