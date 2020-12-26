### string关于字符串长度的详解

string 类型对象包括三种求解字符串长度的函数：==**size(),length()**==,maxsize(),capacity()

- size() 和 length()：这两个函数会返回 string 类型对象中的字符个数，且它们的执行效果相同。
- max_size()：max_size() 函数返回 string 类型对象最多包含的字符数。一旦程序使用长度超过 max_size() 的 string 操作，编译器会拋出 length_error 异常。
- capacity()：该函数返回在重新分配内存之前，string 类型对象所能包含的最大字符数。

 string 类型对象还包括一个 reserve() 函数。调用该函数可以为 string 类型对象重新分配内存。重新分配的大小由其参数决定。reserve() 的默认参数为 0。 

```c++
#include <iostream>
#include <string>
using namespace std;
int main (){
    int size = 0;
    int length = 0;
    unsigned long maxsize = 0;
    int capacity=0;
    string str ("12345678");
    string str_custom;
    str_custom = str;
    str_custom.reserve (5)
    //str_custom.resize (5);
    size = str_custom.size();
    length = str_custom.length();
    maxsize = str_custom.max_size();
    capacity = str_custom.capacity();
    cout << "size = " << size << endl;
    cout << "length = " << length << endl;
    cout << "maxsize = " << maxsize << endl;
    cout << "capacity = " << capacity << endl;
    return 0;
}
```

```latex
size = 8
length = 8
maxsize = 2147483647
capacity = 15 
```

​	由此程序可知，string 类型对象 str_custom 调用 reserve() 函数时，似乎并没有起到重新分配内存的目的 

修改上述代码，删除语句 str_custom.reserve (5)，在代码 str_custom = str 之后如下添加代码：

```c++
 str_custom.resize (5); 
```

修改后程序的执行结构如下：

```latex
size = 5
length = 5
maxsize = 2147483647
capacity = 15 
```

重新设置 string 类型对象 str_custom 的大小之后，重新求解 str_custom 的大小，其执行效果与设置的数值一致（均为 5）。

