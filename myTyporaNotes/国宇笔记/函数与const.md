#### const函数与非const函数重载

```c++
#include <stdio.h>
class A{
private:
    mutable int aa;
public:
    A(){}
    int x(){
        printf("no const\n");
        return aa++;
    }
    int x() const{//不能对函数成员进行更改，必须为const型实例才能调用。函数后+const的均有此性质
        printf("const\n");
        return aa++;
    }
};
int main(){
    A a1;
    a1.x();
    const A a2;
    a2.x();   
    return 0;
}
```

```out
no const
const
```

```c++
#include <stdio.h>
class A{
private:
    mutable int aa;
public:
    A(){}
    int x(const int tmp){
        printf("no const\n");
        aa=tmp;
        return aa++;
    }
    int x(int tmp) {
        printf("const\n");
        aa=tmp;
        return aa++;
    }
};
int main(){
    A a1;
    a1.x(1);
    A a2;
    a2.x(1);   
    return 0;
}
```

```c++
main.cpp:12:9: error: ‘int A::x(int)’ cannot be overloaded with ‘int A::x(int)’
   12 |     int x(int tmp) {
      |         ^
main.cpp:7:9: note: previous declaration ‘int A::x(int)’
    7 |     int x(const int tmp){
      |        
```

