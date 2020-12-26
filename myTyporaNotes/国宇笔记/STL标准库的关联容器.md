### STL标准库的关联容器

| 按关键字有序保存元素 |                                  |
| -------------------- | -------------------------------- |
| map                  | 关联数组；保存关键字-值对        |
| set                  | 关键字即值，即只保存关键字的容器 |
| multimap             | 关键字可重复出现的map            |
| multiset             | 关键字可重复出现的set            |
| 无序集合             |                                  |
| unordered_map        | 用哈希函数组织的map              |
| unordered_set        | 用哈希函数组织的set              |
| unordered_multimap   | 哈希组织的map,关键字可重复出现   |
| unordered_multiset   | 哈希组织的set,关键字可重复出现   |

### pair类型

- 标准库类型。
- #include<utility>
- pair是将2个数据组合成一组数据，如STL中的map就是将key和value放在一起来保存。
- 当一个函数需要返回2个数据的时候，可以选择pair。 
- **pair的实现是一个结构体**，主要的两个成员变量是`first` ，`second` 因为是使用struct不是class，所以可以直接使用pair的成员变量,不必构造对象。 

#### pair类型的定义与初始化

- `pair<T1, T2>  p;` ： 定义了一个空的pair对象p，T1和T2的成员都进行了值初始化
- `pair<T1, T2>  p(v1, v2);` ： p是一个成员类型为T1和T2的pair; first和second成员分别用v1和v2进行初始化。
- `pair<T1, T2>  p = {v1, v2}` ：等价于p(v1, v2)
- `make_pair(v1, v2)` ： 以v1和v2值创建的一个新的pair对象

#### 常用操作

| `p.first`     | 返回p的名为 first 的(公有)数据成员                           |
| ------------- | ------------------------------------------------------------ |
| `p.second`    | 返回p的名为second的(公有)数据成员                            |
| `p1 relop p2` | 关系运算符 (<、>、<= 、>=) 按字典序定义。例如，当 `p1.first < p2.first` 或 `!(p2.first < p1.first) && p1.second < p2.second` 成立时， `p1 < p2 为 true`。关系运算利用元素的 < 运算符来实现 |
| p1 == p2      | 当 first 和 second 成员分别相等时，两个pair相等              |
| p1 != p2      | 若不能达到以上要求，则不相等                                 |

```c++
#include <stdio.h>
#include <string.h>
#include <string>
#include <utility>
using namespace std;
int main(){
    pair<int, string> p1(0, "Hello");
    printf("%d, %s\n", p1.first, p1.second.c_str());
    pair<int, string> p2 = make_pair(1, "World");
    printf("%d, %s\n", p2.first, p2.second.c_str());
    return 0;
}
```

```latex
0, Hello
1, World
```

### map

#### 头文件

#include<map>

#### 创建

map是键-值对的组合，即map的元素是pair，其有以下的一些定义的方法：

- `map m;` ： 定义了一个名为m的空的map对象
- `map m2(m);` ： 创建了m的副本m2
- `map m3(b, e);` ： 创建了map对象m3，并且存储迭代器b和e范围内的所有元素的副本

map的 **`value_type`** 是存储元素的键以及值的pair类型，键为const。

```c++
//value_type只是多进行了一次包装，类似以下定义
template <typename T> class Bag //包装容器袋子
{
public:
    typedef T value_type;
    T val; //内容
    Bag(T value):val(value){};

};
```

```c++
map<int, char> m; 						// 定义了一个名为m的空的map
map<int, char> m2(m); 					// 创建了m的副本m2
map<int, char> m3(m.begin(), m.end()); 	// 创建了map对象m3，并且存储迭代器范围内的所有元素的副本
```

#### 元素访问

 **注意：**下标[] 和 at() 操作，只使用与非 const 的 map 和 unordered_map

#####  使用下标 [ ] 访问

```c++
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<char, std::string> mymap;

    mymap['a'] = "an element";
    mymap['b'] = "another element";
    mymap['c'] = mymap['b'];
    
    std::cout << "mymap['a'] is " << mymap['a'] << '\n';
    std::cout << "mymap['b'] is " << mymap['b'] << '\n';
    std::cout << "mymap['c'] is " << mymap['c'] << '\n';
    std::cout << "mymap['d'] is " << mymap['d'] << '\n';
    
    std::cout << "mymap now contains " << mymap.size() << " elements.\n";
    return 0;

}
/*
mymap['a'] is an element
mymap['b'] is another element
mymap['c'] is another element
mymap['d'] is 					// 下标访问不会进行下标检查
mymap now contains 4 elements.
*/
```

 **注意：**下标访问不会做下标检查，如上第4行打印的语句不会报错，但打印结果为空，因为**下标访问会插入不存在的key，对应的value为默认值**
而使用 at() 访问则会做下标检查，若不存在该key会报错 

#### 使用 at() 方法访问

```c++
#include <iostream>
#include <map>
#include <string>
int main() {
    std::map<std::string, int> mymap = {
        {"alpha", 0}, {"beta", 0}, {"gamma", 0}};
    mymap.at("alpha") = 10;
    mymap.at("beta") = 20;
    mymap.at("gamma") = 30;
    for (auto& x : mymap) {
        std::cout << x.first << ": " << x.second << '\n';
    }
    return 0;
}
/*
alpha: 10
beta: 20
gamma: 30
*/
```

#### 元素插入

 在map中元素有两种插入方法：

- 使用下标 [] 

- 使用 insert() 函数 

#### 使用下标[]插入

使用下标访问**不存在的元素**，将会在map容器中添加一个新的元素；
使用下标访问**存在的元素**，将会覆盖map容器中的该元素 

```c++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<int, char> mymap;
    mymap[0] = 'a';
    mymap[1] = 'b';
    mymap[2] = 'c';
    mymap[0] = 'x';
    for (map<int, char>::iterator iter = mymap.begin(); iter != mymap.end(); iter++)
        cout << iter->first << " ==> " << iter->second << endl;
    return 0;
}
```

#### 使用insert()插入元素

insert函数的插入方法主要有如下：

- `pair<iterator,bool> insert (const value_type& val);`
  - 插入单个键值对，并返回插入位置和成功标志，插入位置已经存在值时，插入失败
- `iterator insert (const_iterator position, const value_type& val);`
  - 在指定位置插入，在不同位置插入效率是不一样的，因为涉及到重排
- `void insert (InputIterator first, InputIterator last);`
  - 插入迭代器范围内键值对

几种插入方法如下面的例子所示：

#include <iostream>
#include <map>

```c++
int main()
{
    std::map<char, int> mymap;

    // （1）插入单个值
    mymap.insert(std::pair<char, int>('a', 100));
    mymap.insert(std::pair<char, int>('z', 200));
    mymap.insert(std::make_pair('f', 300));	// pair方式和make_pair功能是一样的
    
    // 返回插入位置以及是否插入成功
    std::pair<std::map<char, int>::iterator, bool> ret;
    ret = mymap.insert(std::pair<char, int>('z', 500));
    if (ret.second == false) {
        std::cout << "element 'z' already existed";
        std::cout << " with a value of " << ret.first->second << '\n';
    }
    
    // （2）指定位置插入
    std::map<char, int>::iterator it = mymap.begin();
    mymap.insert(it, std::pair<char, int>('b', 300));  //效率更高
    mymap.insert(it, std::pair<char, int>('c', 400));  //效率非最高
    
    // （3）范围多值插入
    std::map<char, int> anothermap;
    anothermap.insert(mymap.begin(), mymap.find('c'));
    
    // （4）列表形式插入
    anothermap.insert({ { 'd', 100 }, {'e', 200} });
    
    return 0;

}
```

#### erase() 删除元素

从map中删除元素的函数是`erase()`，该函数有如下的三种形式：

- `size_t erase( const key_type& key );`
  - 根据key来进行删除， 返回删除的元素数量，在map里结果非0即1
- `iterator erase( iterator pos )`
  - 删除迭代器指向位置的键值对，并返回一个指向下一元素的迭代器
- `iterator erase( const_iterator first, const_iterator last );`
  - 删除一定范围内的元素，并返回一个指向下一元素的迭代器

```c++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<int, int> mymap;
    for (int i = 0; i < 20; i++) {
        mymap.insert(make_pair(i, i));
    }
    mymap.erase(0);          	 // （1）删除key为0的元素
    mymap.erase(mymap.begin());  // （2）删除迭代器指向的位置元素
    map<int, int>::iterator it;
    for (it = mymap.begin(); it != mymap.end(); it++) {
        cout << it->first << "==>" << it->second << endl;
    }
    return 0;
}
```

####  count(k) 查找关键字k出现的次数

 **`size_type count (const key_type& k) const;`** 

```c++
 mymap.count(1);	// 查找关键字1在容器map中出现的次数，如果不存在则为0 
```

#### find(k) 查找元素

- **`iterator find (const key_type& k);`**
- **`const_iterator find (const key_type& k) const;`**

若存在，返回指向该key的迭代器
若不存在，则返回迭代器的尾指针，即 mymap.end()，即 -1 

```c++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<int, int> mp;
    for (int i = 0; i < 20; i++) {
        mp.insert(make_pair(i, i));
    }

    if (mp.count(0)) {
        cout << "yes!" << endl;
    } else {
        cout << "no!" << endl;
    }
    
    map<int, int>::iterator it_find;
    it_find = mp.find(0);
    if (it_find != mp.end()) {
        it_find->second = 20;
    } else {
        cout << "no!" << endl;
    }
    
    map<int, int>::iterator it;
    for (it = mp.begin(); it != mp.end(); it++) {
        cout << it->first << " ==> " << it->second;
    }
    return 0;

}
```

####  lower_bound(k) 返回关键字>=k的元素的第一个位置(是一个迭代器)

- **`iterator lower_bound (const key_type& k);`**
- **`const_iterator lower_bound (const key_type& k) const;`**

```c++
c.lower_bound(k)
```

#### upper_bound(k) 返回关键字>k的元素的第一个位置(是一个迭代器)

- **`iterator upper_bound (const key_type& k);`**
- **`const_iterator upper_bound (const key_type& k) const;`**

```c++
c.upper_bound(k)
```

> **注意：lower_bound 和 upper_bound 不适用与无序容器**

#### equal_range() 返回一个迭代器pair，表示关键字 == k的元素的范围。若k不存在，pair的两个成员均等于c.end()

- **`pair equal_range (const key_type& k) const;`**
- **`pair equal_range (const key_type& k);`**

```c++
#include <iostream>
#include <map>
using namespace std;

int main() {
    map<char, int> mymap;
    mymap['a'] = 3;
    mymap['b'] = 4;
    mymap['c'] = 5;
    mymap['d'] = 6;
    
    cout << mymap.lower_bound('c')->first << endl;  // 返回key >= 'c'第一个元素的迭代器
    cout << mymap.upper_bound('c')->first << endl;  // 返回key >  'c'第一个元素的迭代器
    
    pair<map<char, int>::iterator, map<char, int>::iterator> ret;
    ret = mymap.equal_range('c');
    cout << "lower bound points to: ";
    cout << ret.first->first << " => " << ret.first->second << '\n';
    
    cout << "upper bound points to: ";
    cout << ret.second->first << " => " << ret.second->second << '\n';
    return 0;

}
/*
c
d
lower bound points to: c => 5
upper bound points to: d => 6
*/
```

####  empty() 容器是否为空

```c
mymap.enpty();
```

#### clear() 清空容器

```c
mymap.clear();
```

#### size() 容器的大小

```c
mymap.size();
```

#### max_size() 容器可以容纳的最大元素个数

```c
mymap.max_size();
```

####  **swap**() 交换两个map

```c
A.swap(B);
```

####  begin() 返回指向map头部的迭代器

#### end() 返回指向map末尾的迭代器

#### rbegin() 返回一个指向map尾部的逆向迭代器

#### rend() 返回一个指向map头部的逆向迭代器

#### key_comp() 比较key_type值大小

```c++
 // 比较两个关键字在map中位置的先后
 key_compare key_comp() const; 
```

```c++
map<char,int> mymap;
map<char,int>::key_compare mycomp = mymap.key_comp();
mymap['a']=100;
mymap['b']=200;
mycomp('a', 'b');  // a排在b前面，因此返回结果为true
```

#### value_comp() 比较value_type值大小

```c++
#include <iostream>
#include <map>

int main() {
    std::map<char, int> mymap;

    mymap['x'] = 1001;
    mymap['y'] = 2002;
    mymap['z'] = 3003;
    
    std::cout << "mymap contains:\n";
    std::pair<char, int> highest = *mymap.rbegin();  // last element
    std::map<char, int>::iterator it = mymap.begin();
    do {
        std::cout << it->first << " => " << it->second << '\n';
    } while (mymap.value_comp()(*it++, highest));	// 注意这里只会比较value_type中的key
    
    return 0;
}
```

#### map遍历

##### 使用迭代器遍历

```c++
#include <iostream>
#include <string>
#include <map>
using namespace std;
int main() {
    map<string, int> word_count;
    string word;
    while (cin >> word && word != "-1")		// 统计每个单词出现的次数
        word_count[word]++;
    
    // 使用迭代器遍历
    map<string, size_t>::iterator iter;
    for (iter = word_count.begin(); iter != word_count.end(); iter++) {
        cout << iter->first << " occurs " << iter->second
             << ((iter->second) > 1 ? " times" : " time") << endl;
    }
    
    // 当key是int类型的话，还可以使用下标迭代访问
    return 0;
}
```

### set

set是STL中一种标准关联容器。

- 它底层使用平衡的搜索树——红黑树实现，插入删除操作时仅仅需要指针操作节点即可完成，不涉及到内存移动和拷贝，所以效率比较高。
- set，顾名思义是“集合”的意思，在set中元素都是唯一的，而且默认情况下会对元素自动进行升序排列
- 支持集合的交(set_intersection),差(set_difference) 并(set_union)，对称差(set_symmetric_difference) 等一些集合上的操作，
- 如果需要集合中的元素允许重复那么可以使用multiset。

1. set容器的常用操作

```c++
使用时注意包含头文件<set>    std::set and std::multiset associative containers
s.begin()     　返回set容器的第一个元素
s.end() 　　　　 返回set容器的最后一个元素
s.clear()       删除set容器中的所有的元素
s.empty() 　　　 判断set容器是否为空
s.insert()      插入一个元素
s.erase()       删除一个元素
s.size() 　　　　返回当前set容器中的元素个数
```

 set模板原型：//Key为元素(键值)类型 

```Cpp
template <class Key, class Compare=less<Key>, class Alloc=STL_DEFAULT_ALLOCATOR(Key) >
```

2. set容器的创建 

```cpp
#include <iostream>
#include <set>
#include <functional>
using namespace std;
set<int> s;

int main(){
   set<int > seta; //默认是小于比较器less<int>的set

   set<int, greater<int> > setb; //创建一个带大于比较器的set，需包含头文件functional

   int a[5] = {1,2,3,4,5};
   set<int > setc(a,a+5); //数组a初始化一个set；

   set<int > setd(setc.begin(),setc.end()); //setc初始化一个set
   //上述两例均为区间初始化

   set<int > sete(setd); //拷贝构造创建set
   return 0;
}
```

3. set容器的增删改查 

   1). 增

```cpp
#include <iostream>
#include <set>
using namespace std;
set<int >s;
void setprint(int cnt){
cout << "Test output :" << cnt << ":" << endl;
for(set<int>::iterator it = s.begin(); it!= s.end(); it++)
cout << *it << " ";
puts("");
return ;
}
int main(){
int cnt = 1;
s.insert(1);
s.insert(2);
s.insert(5);
setprint(cnt++);

s.insert(2); //set只允许用一个值出现一次，要插入相同元素请用multiset
setprint(cnt++);

int a[4] = {11,12,13,14};
s.insert(a,a+4); //将区间[a, a+4]里的元素插入容器
setprint(cnt++);

return 0;

}
```

   ```out
   Test output :1:
   1 2 5
   Test output :2:
   1 2 5
   Test output :3:
   1 2 5 11 12 13 14
   
   D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 10768)已退出，代码为 0。
   按任意键关闭此窗口. . .
   ```

2). 删

```c++
s.erase()       删除一个元素
s.clear()       删除set容器中的所有的元素
```

```cpp
#include <iostream>
#include <set>
using namespace std;
set<int >s;
void setprint(int cnt){
    cout << "Test output :" << cnt << ":" << endl;
    for(set<int>::iterator it = s.begin(); it!= s.end(); it++)
        cout << *it << " ";
    puts("");
    return ;
}

int main(){
    int cnt = 1;
    for(int i = 1; i < 11; i++){
        s.insert(i);
    }
    setprint(cnt++);

    s.erase(9); //根据元素删除
    setprint(cnt++);
     
    set<int>::iterator ita = s.begin();
    set<int>::iterator itb = s.begin();
    s.erase(ita);  //删除迭代器指向位置的元素
    setprint(cnt++);
     
    ita = s.begin();
    itb = s.begin();
    itb++;itb++;
    s.erase(ita,itb); //删除区间[ita,itb)的元素
    setprint(cnt);
    s.clear();
    return 0;

}
```

```out
Test output :1:
1 2 3 4 5 6 7 8 9 10
Test output :2:
1 2 3 4 5 6 7 8 10
Test output :3:
2 3 4 5 6 7 8 10
Test output :4:
4 5 6 7 8 10

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 13096)已退出，代码为 0。
按任意键关闭此窗口. . .
```

3). 改

**不能直接修改容器内数据，所以只能删除某元素再插入要修改的数值。**

4). 查

```cpp
s.find()        查找一个元素，如果容器中不存在该元素，返回值等于s.end()
```

```c++
#include <iostream>
#include <set>
using namespace std;
set<int >s;
void setprint(int cnt){
    cout << "Test output :" << cnt << ":" << endl;
    for(set<int>::iterator it = s.begin(); it!= s.end(); it++)
        cout << *it << " ";
    puts("");
    return ;
}
int main(){
    int cnt = 1;
    s.insert(1);
    s.insert(2);
    s.insert(5);
    setprint(cnt++);

    if(s.find(2) != s.end()) 
        cout << "2 is existent" << endl;
    else 
        cout << "2 is non-existent" << endl;
    if(s.find(3) == s.end()) 
        cout << "3 is non-existent" << endl;
    else 
        cout << "2 is existent" << endl;
    return 0;

}
```

```out
Test output :1:
1 2 5
2 is existent
3 is non-existent

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 12904)已退出，代码为 0。
按任意键关闭此窗口. . .
```

其他操作

```c++
s.lower_bound()//返回第一个大于或等于给定关键值的元素
s.upper_bound()// 返回第一个大于给定关键值的元素
s.equal_range()//返回一对定位器，分别表示 第一个大于或等于给定关键值的元素 和 第一个大于给					定关键值 的元素，这个返回值是一个pair类型，如果这一对定位器中哪个返回失				   败，就会等于s.end()
```

```c++
#include <iostream>
#include <set>
using namespace std;

int main(){
    set<int> s;
    s.insert(1);
    s.insert(2);
    s.insert(5);

    cout << "lower_bound & upper_bound test:" << endl;
     
    cout << "第一个大于或等于3的元素: " << *s.lower_bound(3) << endl;
    cout << "第一个大于或等于2的元素: " <<*s.lower_bound(2) << endl;
    cout << "第一个大于2的元素: " <<*s.upper_bound(2) << endl;
     
    cout << "equal_range test:" << endl;
     
    cout << "第一个大于或等于2的元素: " <<  *s.equal_range(2).first << endl;
    cout << "第一个大于2的元素: " << *s.equal_range(2).second << endl;
    return 0;

}
```

```OUT
lower_bound & upper_bound test:
第一个大于或等于3的元素: 5
第一个大于或等于2的元素: 2
第一个大于2的元素: 5
equal_range test:
第一个大于或等于2的元素: 2
第一个大于2的元素: 5

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 8004)已退出，代码为 0。
按任意键关闭此窗口. . .
```

```cpp
 判断元素是否在set中 & 判断set是否为空 

#include <iostream>
#include <set>
#include <functional>
using namespace std;

int main(){
    set<int > s;
    if(s.empty()) cout << "容器为空" << endl;
    s.insert(1);
    if(!s.empty()) cout << "容器不为空" << endl;

    if(s.count(1)) cout << "1在容器中" << endl;
    if(!s.count(2)) cout << "2不在容器中" << endl;
    return 0;

}
```

```OUT
容器为空
容器不为空
1在容器中
2不在容器中

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 12036)已退出，代码为 0。
按任意键关闭此窗口. . .
```

 自定义比较函数 

```c++
#include <iostream>
#include <set>
#include <functional>
using namespace std;

struct cmp{
    bool operator () (int a,int b) const {//a,b前不能加&，const位置注意
        return a > b;
    }
};
set<int, cmp>s; //自定义排序函数构造set
void setprint(int cnt){
    cout << "Test output :" << cnt << ":" << endl;
    for(set<int,cmp>::iterator it = s.begin(); it!= s.end(); it++)
        cout << *it << " ";
    puts("");
    return ;
}
int main(){
    s.insert(1);
    s.insert(2);
    s.insert(6);
    setprint(1);
    return 0;
}
```

```out
Test output :1:
6 2 1

D:\Desktop\Important\vs2019_pgt\zhugyTest\Debug\zhugyTest.exe (进程 12176)已退出，代码为 0。
按任意键关闭此窗口. . .
```

