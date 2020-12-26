### CString,cstring,string

#### **string与cstring有什么区别**

​	<string>是C++标准库头文件，包含了拟容器class std::string的声明（不过class string事实上只是basic_string<char>的typedef），用于字符串操作。

​	 <cstring>是C标准库头文件<string.h>的C++标准库版本，包含了C风格字符串（NUL即'\0'结尾字符串）相关的一些类型和函数的声明，例如strcmp、strchr、strstr等。<cstring>和<string.h>的最大区别在于，其中声明的名称都是位于std命名空间中的，而不是后者的全局命名空间。
​	看定义就知道了，string是新标准，定义了namespace std;而cstring虽然也是新标，但是定义中包含的是string.h。 

 string中可以进行+ = += >等运算，而cstring中不能进行相关运算。 

```c++
#include <cstring>  //不可以定义string s；可以用到strcpy等函数
using  namespace  std;

#include <string>  //可以定义string s；可以用到strcpy等函数
using  namesapce  std;

#include <string.h>  //不可以定义string s；可以用到strcpy等函数
```

1）文件cstring，和string.h对应，c++版本的头文件，包含比如strcpy之类的字符串处理函数
2）文件string.h，和cstring对应，c版本的头文件，包含比如strcpy之类的字符串处理函数
3）文件string，包含std::string的定义，属于STL范畴
4）CString，MFC里的的字符串类

string.h是C语言中字符串操作函数的头文件

cstring是c++对C语言中的strcpy之类的函数申明，包含cstring之后，就可以在程序中使用C语言风格的strcpy之类的函数。

string是c++语言中string类模板的申明 

CString是MFC中定义的字符串类，MFC中很多类及函数都是以CString为参数的，另外CString类重载了（LPCSTR）运算符，所以如果你在MFC下面使用CString类，就可以直接用CString类做为参数来调用需要一个C语言风格字符串的win  api函数，编译器会自动调用(LPCSTR)成员函数完成从CString到一个C风格字符串的转换。如果你在MFC下使用C++语言中标准的 string类，那么在调用需要C语言风格的字符串为参数的win  api时，你必须显示调用sting.c_str()成员函数，来完成同样的转换，也就是说在使用MFC里，如果用CString类，会比sting类方便那么一点点。

####  首先cstring与string.h:

cstring和string.h其实里面都是C标准库提供的东西，某些实现中cstring的内容
就是: 
 namespace  std 
 { 
 \#include  <string.h> 
 } 
cstring是C++的组成部分，它可以说是把C的string.h的升级版，但它不是C的组成部分。
所以**如果你用的是C++，那么请用cstring,如果你用的是C请用string.h**。 

​	一般一个C++库老的版本带“.h”扩展名的库文件，比如iostream.h，在新标准后的标准库中都有一个不带“.h”扩展名的相对应，区别除了后者的好多改进之外，还有一点就是后者的东东都塞进了“std”名字空间中。   
​	string，它是C++定义的std::string所使用的文件，是string类的头文件，属于STL范畴。它有很多对字符串操作的方法。 

​	string.h是C++标准化（1998年）以前的C++库文件，在标准化过程中，为了兼容以前，标准化组织将所有这些文件都进行了新的定义，加入到了标准库中，加入后的文件名就新增了一个"c"前缀并且去掉了.h的后缀名，所以string.h头文件成了cstring头文件。但是其实现却是相同的或是兼容以前的。相当于标准库组织给它盖了个章，说“你也是我的标准程序库的一份子了” 

 cstring代表的是string.h，但是被封装到了std里面，譬如调用strlen函数，需要写成std::strlen (yourstr)才行，这个使用方法比较符合C++的标准要求string就是C++标准库里面的string模板（确切地说应该是一个特化的模板），但是他同样包含了C风格字符串操作函数的定义（应该是通过包含string.h实现的）string.h就不需要使用名字空间了，这个是C风格字符串操作的一个函数库，strlen，strcpy，strcat，strcmp……都在这里面了，不过既然是C风格的库，当然不需要namespace支持了。 