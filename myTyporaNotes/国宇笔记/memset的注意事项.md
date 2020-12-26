### memset的注意事项

```c++
#include <iostream>
using namespace std;
int main() {
	int array[5];
	memset(array, 0x3f, sizeof(array));
	for (int i = 0; i < 5; i++) {
		cout << array[i] << endl;
	}
	return 0;
}
```

```out
1061109567
1061109567
1061109567
1061109567
1061109567

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 4952)已退出，代码为 0。
按任意键关闭此窗口. . .
```

#### memset的函数原型为：

```c++
void *memset(void *str, int c, size_t n)
```

 memset 是按字节赋值的。其实就是取第二个参数c进行分析，int是32为的，进行赋值时只对低8位的那个字节的数据进行size_t范围内的赋值，c为0x3f时，相当于array的每一项仅是0x3f3f3f3f,即是1061109567。

#### 总结：

c的取值不是只有0与-1,建议写成十六进制形式及0x后面加两位数，表示一个字节的数。当然对于上述0x3也可写成c为十进制63（十六进制为0x3f)进行替换。

#### 举例分析：

#### 1.对于数组初始化为0操作

常用：

```c++
memset(a,0,sizeof a);
```

替代循环：

```c++
 for(i=0;i<m;i++) a[i]=0; 
```

缩短运行时间

#### 2.将数组初始化为无穷大的情况

通常对于 32位int有符号数，我们将 无穷大INF 设为 0x3f3f3f3f ，

#define INF 0x3f3f3f3f

为什么不设 INF 为最大值 0x7fffffff （32位int有符号数）呢？

原因是对于部分问题可能出现 无穷大加无穷大的情况，这样就会出现溢出错误。

所以至多应选择 0x7fffffff 的一半 0x3fffffff，

而 0x3fffffff 和 0x3f3f3f3f 在数量级上是差不多，

都足够大，但将INF设为 0x3f3f3f3f 就可以用 memset 来初始化。

```c++
memset(a,0x3f,sizeof a);
```

memset 是按字节进行赋值，能将数组整个初始化为 3f3f3f3f3f3f3f3f3f3f3f3f3f...... ，也就是无穷大了

这样解决的问题，就不必先跑一边O(n^2^)去初始化数组。

#### 3.类似的，需将数组初始化为-1时

可以用：

```c++
memset(a,0xff,sizeof a);
```

这种情况也很常见。

初学时，很多猿不理解为什么memset只对初始化 0 和 -1 时有效。

```c++
memset(a,0,sizeof (a));
memset(a,-1,sizeof (a));
```

因为 memset 是按字节赋值，即使给它int类型数，它也是只取该数最低位的一个字节来赋值。

所以 memset 传int型参数来初始化只适用于各个字节都相同的数，这样才能表现出期望的效果，

就像 0x3f3f3f3f 0xffffffff 0x00000000 这种。

#### long long型数据

以上初始化操作对于 64位 long long 有符号数 同样适用。

```c++
#define ll_INF 0x3f3f3f3f3f3f3f3f
```

