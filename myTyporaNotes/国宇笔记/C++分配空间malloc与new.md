### C++中有了malloc / free , 为什么还需要 new / delete？

- malloc与free是C++/C语言的**标准库函数**，new/delete是C++的**运算符**。它们都可用于申请动态内存和释放内存。
- 对于非内部数据类型的对象而言，光用maloc/free无法满足动态对象的要求。对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。
- 由于malloc/free是库函数而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加于malloc/free。因此C++语言需要一个能完成动态内存分配和初始化工作的运算符new，以一个能完成清理与释放内存工作的运算符delete。注意new/delete不是库函数。
- 最后补充一点体外话，new 在申请内存的时候就可以初始化（如下代码）， 而malloc是不允许的。另外，由于malloc是库函数，需要相应的库支持，因此某些简易的平台可能不支持，但是new就没有这个问题了，因为new是C++语言所自带的运算符。

```c++
int *p = new int(1);
```

特别的，在C++中，如下的代码，用new创建一个对象(new 会触发构造函数， delete会触发析构函数)，但是malloc仅仅申请了一个空间，所以在C++中引入new和delete来支持面向对象。

```c++
#include <cstdlib>
class Test{
    ...
}
Test* pn = new Test;
Test* pm = (Test*)malloc(sizeof(Test));
```

```c++
int* p = new int[10]();//初始化为0，且不能填写其他值，0也不行
//可以int* p1 = new int[10];////////内存单元里的值未知/////////
//int* p2 = new int(2);//
cout << p[0] << endl << p[1] << endl;
/*
0
0
*/
```

