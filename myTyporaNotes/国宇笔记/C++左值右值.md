### 左值右值

#### 【官方定义】

1. 一个左值表达式的求值结果是一个对象或者一个函数，而某些右值表达式的求值结果也是对象；

2. 以常量对象为代表的**某些左值**并不能作为赋值语句的**左侧**运算对象。

   归纳：当一个对象被用作右值的时候，用的是对象的值（内容）；而被用作左值的时候，用的是对象的身份（在内存中的位置）。

#### **第一句解读：**

- 左值：能用“取地址&”运算符获得对象的内存地址
- 右值：不能用“取地址&”运算符获得对象的内存地址
- 特例：因为可以用&取得字符串字面值常量的地址，所以它是一个左值；而其他的字面值常量则不可用&取地址，所以它们是右值

我们知道对象是一块空间，那么有哪些对象以及为什么不能用&取地址呢？

- 对于**临时对象**，它可以存储于**寄存器**中，所以没办法用“取地址&”运算符；

- 对于（**非字符串**）**常量**，它可能被编码到机器指令的“立即数”中，所以没办法用“取地址&”运算符。

#### **解读第二句：**

- 字符串字面值常量确实不能放在等号左边啊，没错啊！

- 解读归纳：所以啊，左值看地址，右值看内容。

```c++
#include <iostream>
using namespace std;
int& func(int& a){
	return a;           // 不能返回局部对象的引用
}
int main(){
	int a = 10;
	func(a) = 100;          // 返回非常量引用的函数可以当作左值使用
	cout << a << endl;      // 输出100
	return 0;
}
```

