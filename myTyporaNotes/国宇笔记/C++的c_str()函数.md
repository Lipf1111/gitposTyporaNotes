C++的c_str()函数

**const char *c_str();** 
c_str()函数返回一个**指向正规C字符串的指针常量**, 内容与本string串相同. 
		这是为了与c语言兼容，在c语言中没有string类型，故必须通过string类对象的成员函数c_str()把string 对象转换成c中的字符串样式。 

1.c_str是一个内容为字符串指向字符数组的**临时指针**； 
2.c_str返回的是一个**可读不可改**的常指针；

注意：一定要**使用strcpy()函数**等来操作方法c_str()返回的指针 
比如：**最好不要这样:**

char* c; 
string s="1234"; 
c = s.c_str(); 

 c最后指向的内容是垃圾，因为s对象被析构，其内容被处理，同时，编译器也将报错——将一个const char \*赋于一个char\* 。 

 **应该这样用：** 

```c++
char c[20]; //不能为char* c;必须为c分配地址，可以为char *c=new char[20];
string s="1234"; 
strcpy(c,s.c_str()); //strcpy(c,s.data());
```

这样才不会出错，c_str()返回的是一个临时指针，不能对其进行操作

再举个例子 
c_str() 以 char\* 形式传回 string 内含字符串 
如果一个函数要求char\*参数，可以使用c_str()方法：

string s = "Hello World!";
printf("%s", s.c_str()); //输出 "Hello World!"

