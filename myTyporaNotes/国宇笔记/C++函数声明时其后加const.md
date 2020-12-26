### C++中函数声明时函数名后面加const

```c++
#include "stdafx.h"
#include <iostream>
using namespace std;
class TEST{
    int num;
public:
    TEST(){
        int b =10;
        num = b;
    };
    ~TEST(){};
    void out1(){
        cout<<num<<endl;
    }
    void out2() const{
        cout<<num<<endl;
    }
    void out3() const{
        num+=10; //出错，const函数不能修改其数据成员
        cout<<num<<endl;
    }
};
int _tmain(int argc, _TCHAR* argv[]){
    TEST a1;
    a1.out1();
    a1.out2();
    a1.out3();
    const TEST a2;
    a2.out1(); // 错误，const的成员 不能访问非const的函数
    a2.out2();
    a2.out3();
    return 0;
}
```

在类成员函数的声明和定义中，

**const的函数不能对其数据成员进行修改操作。**

**const的对象，不能引用非const的成员函数。**



通过把类成员函数声明为const  以表明它们不修改类对象。

任何不会修改数据成员的函数都应该声明为const类型。如果在编写const成员函数时，不慎修改了数据成员，或者调用了其它非const成员函数，编译器将指出错误，这样做的好处是提高程序了的健壮性。

