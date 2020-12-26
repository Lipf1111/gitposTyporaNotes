### string与char*转换

#### string-->char*

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	std::string s = "hello world";
	char c[20];//不能为char* c;或char* c=NULL;提示必须初始化；
	strcpy(c, s.c_str());//strcpy(c,s.data());
	cout << c << endl;
}//正常打印
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	std::string str = "hello world";
	char* chr = const_cast<char*>(str.c_str());
	cout << chr << endl;
}//正常打印
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	string str = "hello world";
	char* chr = &str[0];
	cout << chr << endl;
}//正常打印
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	std::string str = "hello world";
	const char* chr = str.c_str();
	cout << chr << endl;
}//正常打印
```

```C++
#include <iostream>
#include <string>
using namespace std;
int main() {
	std::string str = "hello world";
	char* chr = new char[str.length() + 1];
	strcpy(chr, str.c_str());
	cout << chr << endl;
	delete[] chr;
}
//正常打印
```

> 另：

在用vs2019测试时产生**错误**：**error C4996**: 'strcpy': This function or variable may be unsafe. Consider using strcpy_s instead.

**原因**：strcpy()和strcat()函数不安全造成的溢出。

**解决方法**是：【项目属性】->【C++】->【预处理器】->编辑->加入一段代码：_CRT_SECURE_NO_WARNINGS。

>  注意：

```c++
char* c; 
string s="1234"; 
c = s.c_str(); 
```

提示不能将const char\*转换成char\*类型;c_str()返回值类型为const char* 即只读不可修改的指针。

#### char\* -->string

直接赋值

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	char c[] = "this is a char array";
	const char* t = "const char";
	string s = t;
	string ss = c;
	cout << s << endl;
	cout << ss << endl;
}
//正常打印
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	string s;
	char* p = "hello";
	s = p;
	cout << p << endl;
}//报错
//提示第6行错误：error C2440: “初始化”: 无法从“const char [6]”转换为“char *”
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	string s;
	char p[] = "hello";
	s = p;
	cout << p << endl;
}//正常打印
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	const char* chr = "this is a const exp";
	string s(chr, chr + strlen(chr));
	cout << s << endl;
}//正常打印
```

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	string s="hello";
	char* ch = const_cast<char*>(s.c_str());
	cout << ch << endl;
}//正常打印
```

> 附： char* 和const char*

```c++
#include <iostream>
#include <string>
using namespace std;
int main() {
	const char* src = "this is a const exp";
	char* ch = const_cast<char*>(src);
	const char* dst = static_cast<const char*>(ch);
	cout << ch << endl;
	cout << dst << endl;
}//正常打印
//this is a const exp
//this is a const exp
```

参考几篇大佬们的文章，并用vs2019 Community进行测试。网页翻来覆去的只记得下边传送门了。

 https://blog.csdn.net/grllery/article/details/89043618 