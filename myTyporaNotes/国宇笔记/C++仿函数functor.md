### 仿函数

​	

**仿函数的主要功能是为了搭配STL算法使用，单独使用仿函数的情况比较少。**
	functor的英文解释为something that performs a function，即其行为类似函数的东西。仿函数（functors）在C++标准中采用的名称是函数对象（function objects）。仿函数主要用于STL中的算法中，虽然函数指针虽然也可以作为算法的参数，但是函数指针不能满足STL对抽象性的要求，也不能满足软件积木的要求–函数指针无法和STL其他组件搭配，产生更灵活变化。仿函数本质就是类重载了一个operator()，创建一个行为类似函数的对象。 

对于重载了`()`操作符的类，可以实现类似函数调用的过程，所以叫做仿函数，实际上仿函数对象仅仅占用1字节，因为内部没有数据成员，仅仅是一个重载的方法而已。实际上可以通过传递函数指针实现类似的功能，但是为了和STL内部配合使用，他提供了仿函数的特性。 

```c++
struct MyPlus{
    int operator()(const int &a , const int &b) const{
        return a + b;
    }
};

int main(){
    MyPlus a;
    cout << MyPlus()(1,2) << endl;//1、通过产生临时对象调用重载运算符
    cout << a.operator()(1,2) << endl;//2、通过对象显式调用重载运算符
    cout << a(1,2) << endl;//3、通过对象类似函数调用 隐示地调用重载运算符
    return 0;
}
```

```out
3
3
3

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 7952)已退出，代码为 0。
按任意键关闭此窗口. . .
```

​	在STL中，当需要传递仿函数对象的时候，通过采用上述第1种方法，因为传递进去仅仅为了给容器内部成员赋值，所以没有必要生成对象，产生临时对象即可。 

**仿函数的主要功能是为了搭配STL算法使用，单独使用仿函数的情况比较少。**
仿函数（functors）在C++标准中采用的名称是函数对象（function objects）。仿函数主要用于STL中的算法中，虽然函数指针虽然也可以作为算法的参数，但是函数指针不能满足STL对抽象性的要求，也不能满足软件积木的要求–函数指针无法和STL其他组件搭配，产生更灵活变化。仿函数本质就是类重载了一个operator()，创建一个行为类似函数的对象。

对于重载了`()`操作符的类，可以实现类似函数调用的过程，所以叫做仿函数，实际上仿函数对象仅仅占用1字节，因为内部没有数据成员，仅仅是一个重载的方法而已。实际上可以通过传递函数指针实现类似的功能，但是为了和STL内部配合使用，他提供了仿函数的特性。

#### 2、STL中基础仿函数

- 仿函数定义自己型别
  算法内部可能需要使用仿函数返回值或者输出值的类型参数，因此定义两个类。

```c++
//一元函数的参数和返回值类型，通常被继承
template <class Arg, class Result>
struct unary_function {
    typedef Arg argument_type;
    typedef Result result_type;
};

//二元函数的参数和返回值类型，通常被继承
template <class Arg1, class Arg2, class Result>
struct binary_function {
    typedef Arg1 first_argument_type;
    typedef Arg2 second_argument_type;
    typedef Result result_type;
};  
```

上述两个类分别定义了一元和二元函数参数和返回值型别，对应的仿函数类仅仅需要继承此类即可。 

- 算术类仿函数
  标准库给我们定义了一些通用的仿函数，可以直接调用，生成对象，面向用户。 

```c++
//////算术类仿函数 + - * / %////////////////////////////////
//plus仿函数，生成一个对象，里面仅仅有一个函数重载的方法。
template <class T>
struct plus : public binary_function<T, T, T> {
    T operator()(const T& x, const T& y) const { return x + y; }
};

//minus仿函数
template <class T>
struct minus : public binary_function<T, T, T> {
    T operator()(const T& x, const T& y) const { return x - y; }
};

template <class T>
struct multiplies : public binary_function<T, T, T> {
    T operator()(const T& x, const T& y) const { return x * y; }
};

template <class T>
struct divides : public binary_function<T, T, T> {
    T operator()(const T& x, const T& y) const { return x / y; }
};
template <class T>
struct modulus : public binary_function<T, T, T> {
    T operator()(const T& x, const T& y) const { return x % y; }
};

//取负值
template <class T>
struct negate : public unary_function<T, T> {
    T operator()(const T& x) const { return -x; }
};
```

单独使用仿函数，通常将仿函数和算法部分单独分开使用。 

```c++
#include <iostream>     // std::cout
#include <functional>   // std::plus
#include <algorithm>    // std::transform
using namespace std;
int main(void)
{
    cout << minus<int>()(10,5) << endl;//5
    cout << multiplies<int>()(10,5) << endl;//50
    cout << divides<int>()(10,5) << endl;//2
    cout << modulus<int>()(10,5) << endl;//0
    cout << negate<int>()(10) << endl;//-10
    return 0;
}
```

```c++
5
50
2
0
-10

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 7820)已退出，代码为 0。
按任意键关闭此窗口. . .
```

 **3、关系运算符仿函数** 

```c++
//关系运算符仿函数////////////////////////////////////////////
// x==y 仿函数
template <class T>
struct equal_to : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x == y; }
};

// x!=y 仿函数
template <class T>
struct not_equal_to : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x != y; }
};
// x>y 仿函数
template <class T>
struct greater : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x > y; }
};
// x<y 仿函数
template <class T>
struct less : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x < y; }
};

// x>=y 仿函数
template <class T>
struct greater_equal : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x >= y; }
};
// x<=y 仿函数
template <class T>
struct less_equal : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x <= y; }
};
```

```c++
// sort algorithm example
#include <iostream>     // std::cout
#include <algorithm>    // std::sort
#include <vector>       // std::vector
#include <functional>   // std::

bool myfunction (int i,int j) { return (i < j); }

int main () {
  int myints[] = {32,71,12,45,26,80,53,33};
  std::vector<int> myvector (myints, myints+8);               // 32 71 12 45 26 80 53 33

  // using default comparison (operator <):
  std::sort (myvector.begin(), myvector.begin()+4);           //(12 32 45 71)26 80 53 33

  // using function as comp
  std::sort (myvector.begin()+4, myvector.end(), myfunction); // 12 32 45 71(26 33 53 80)

  // using object as comp
  std::sort (myvector.begin(), myvector.end(), std::less<int>());     //(12 26 32 33 45 53 71 80)

  // print out content:
  std::cout << "myvector contains:";
  for (std::vector<int>::iterator it=myvector.begin(); it!=myvector.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

```out
myvector contains: 12 26 32 33 45 53 71 80

D:\Desktop\Important\vs2019_pgt\DailyTest\Debug\DailyTest.exe (进程 13136)已退出，代码为 0。
按任意键关闭此窗口. . .
```

 **4、逻辑运算符仿函数** 

```c++
template <class T>
struct logical_and : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x && y; }
};

template <class T>
struct logical_or : public binary_function<T, T, bool> {
    bool operator()(const T& x, const T& y) const { return x || y; }
};

template <class T>
struct logical_not : public unary_function<T, bool> {
    bool operator()(const T& x) const { return !x; }
};
```

 **5、证同、选择、投射仿函数** 

```c++
///////////////////////////////////////////////////////////////////////////////////////////
//证同仿函数，主要用于RB或者hashmap里面 key = value情况
template <class T>
struct identity : public unary_function<T, T> {
  const T& operator()(const T& x) const { return x; }
};

//选择仿函数,主要用与RB和hashmap里面key 不为value情况，从pair中取出key
template <class Pair>
struct select1st : public unary_function<Pair, typename Pair::first_type> {
  const typename Pair::first_type& operator()(const Pair& x) const
  {
    return x.first;
  }
};

//选择仿函数,主要用与RB和hashmap里面key 不为value情况，从pair种取出value
template <class Pair>
struct select2nd : public unary_function<Pair, typename Pair::second_type> {
  const typename Pair::second_type& operator()(const Pair& x) const
  {
    return x.second;
  }
};

//投射函数，输入x和y返回x
template <class Arg1, class Arg2>
struct project1st : public binary_function<Arg1, Arg2, Arg1> {
  Arg1 operator()(const Arg1& x, const Arg2&) const { return x; }
};
//投射函数，输入x和y返回y
template <class Arg1, class Arg2>
struct project2nd : public binary_function<Arg1, Arg2, Arg2> {
  Arg2 operator()(const Arg1&, const Arg2& y) const { return y; }
};
/////////////////////////////////////////////////////////////////////
```



传送门： https://blog.csdn.net/u010710458/article/details/79734558 

