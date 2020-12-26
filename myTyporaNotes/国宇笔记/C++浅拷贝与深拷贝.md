 先考虑一种情况，对一个已知对象进行拷贝，编译系统会自动调用一种构造函数——拷贝构造函数，如果用户未定义拷贝构造函数，则会调用默认拷贝构造函数。

​    先看一个例子，有一个学生类，数据成员时学生的人数和名字：

```cpp
#include <iostream>
using namespace std;
class Student{
private:
    int num;
    char *name;
public:
    Student();
    ~Student();
};
Student::Student(){
    name = new char(20);
    cout << "Student" << endl;
}
Student::~Student(){
    cout << "~Student " << (int)name << endl;
    delete name;
    name = NULL;
}
int main(){
    {// 花括号让s1和s2变成局部对象，方便测试
        Student s1;
        Student s2(s1);// 复制对象
    }
    system("pause");
    return 0;
}
```

![img](.\Pic\SouthEast.jpg)

执行结果：调用一次构造函数，调用两次析构函数，两个对象的指针成员所指内存相同，这会导致什么问题呢？name指针被分配一次内存，但是程序结束时该内存却被释放了两次，会导致崩溃！

​    这是由于编译系统在我们没有自己定义拷贝构造函数时，会在拷贝对象时调用默认拷贝构造函数，进行的是浅拷贝！即对指针name拷贝后会出现两个指针指向同一个内存空间。

![img](.\Pic\SouthEast23.jpg)

​    所以，**在对含有指针成员的对象进行拷贝时，必须要自己定义拷贝构造函数**，使拷贝后的对象指针成员有自己的内存空间，即进行深拷贝，这样就避免了内存泄漏发生。

​    添加了自己定义拷贝构造函数的例子：

```cpp
#include <iostream>
using namespace std;
class Student{
private:
    int num;
    char *name;
public:
    Student();
    ~Student();
    Student(const Student &s);//拷贝构造函数，const防止对象被改变
};
Student::Student(){
    name = new char(20);
    cout << "Student" << endl;
}
Student::~Student(){
    cout << "~Student " << (int)name << endl;
    delete name;
    name = NULL;
}
Student::Student(const Student &s){
    name = new char(20);
    memcpy(name, s.name, strlen(s.name));
    cout << "copy Student" << endl;
}
int main(){
    {// 花括号让s1和s2变成局部对象，方便测试
        Student s1;
        Student s2(s1);// 复制对象
    }
    system("pause");
    return 0;
}
```

![img](.\Pic\SouthEast34234.jpg)

**执行结果：**调用一次构造函数，一次自定义拷贝构造函数，两次析构函数。两个对象的指针成员所指内存不同。 总结：浅拷贝只是对指针的拷贝，拷贝后两个指针指向同一个内存空间，深拷贝不但对指针进行拷贝，而且对指针指向的内容进行拷贝，经深拷贝后的指针是指向两个不同地址的指针。

> 注：当对象中存在指针成员时，除了在复制对象时需要考虑自定义拷贝构造函数，还应该考虑以下两种情形：

1. 当函数的参数为对象时，实参传递给形参的实际上是实参的一个拷贝对象，系统自动通过拷贝构造函数实现；
2. 当函数的返回值为一个对象时，该对象实际上是函数内对象的一个拷贝，用于返回函数调用处。
3. 浅拷贝带来问题的本质在于析构函数释放多次堆内存，使用std::shared_ptr，可以完美解决这个问题。