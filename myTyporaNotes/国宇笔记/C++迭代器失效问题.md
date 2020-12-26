**1、对于序列式容器，比如vector，删除当前的iterator会使后面所有元素的iterator都失效。**

举例如下：

```c++
void vectorTest(){
    vector<int> v;
    for (int i = 0; i < 10; i++){
        v.push_back(i);
    }

    vector<int>::iterator iter;
    for (iter = v.begin(); iter != v.end(); iter++){
        if (*iter > 3)
            v.erase(iter);
    }
    
    for (iter = v.begin(); iter != v.end(); iter++){
        cout << *iter << endl;
    }
}
//报错：vector iterator not incrementable(不可递增)
```

这是因为顺序容器内存是连续分配（分配一个数组作为内存），删除一个元素导致后面所有的元素会向前移动一个位置。（删除了一个元素，该元素后面的所有元素都要挪位置，所以，iter++，已经指向的是未知内存）。
**所以对vector的任何操作，一旦引起控件重新配置，指向原vector的所有迭代器就都失效了。**

> 注：容器会重新分配一个空间将元素放回，在删除原来的内存。

但是erase方法可以返回下一个有效的iterator，这样删除后iter指向的元素后，返回的是下一个元素的迭代器，这个迭代器是vector内存调整过后新的有效的迭代器。

解决方法为：

```c++
void vectorTest(){
    vector<int> v;
    for (int i = 0; i < 10; i++){
        v.push_back(i);
    }

    vector<int>::iterator iter;
    for (iter = v.begin(); iter != v.end();){
        if (*iter > 3) {
            iter = v.erase(iter);
        }else{
            iter++;
        }
    }
    
    for (iter = v.begin(); iter != v.end(); iter++){
        cout << *iter << endl;
    }
}
```

当使用一个容器的insert或者erase函数通过迭代器插入或删除元素"可能"会导致迭代器失效，因此我们为了避免危险，应该获取insert或者erase返回的迭代器，以便用重新获取的新的有效的迭代器进行正确的操作

```c++
iter=vec.insert(iter);
iter=vec.erase(iter);
```

迭代器失效的类型：
 1.由于插入元素，使得容器元素整体“迁移”导致存放原容器元素的空间不再有效，从而使得指向原空间的迭代器失效。
 2.由于删除元素使得某些元素次序发生变化使得原本指向某元素的迭代器不再指向希望指向的元素。

**2.对于链表型数据结构，比如list，使用了不连续分配的内存，删除运算使指向删除位置的迭代器失效，但是不会失效其他迭代器。**

解决办法有两种：
与vector类似，erase(*iter)会返回下一个有效迭代器的值，或者erase(iter++)。

 **3.对于关联容器式(如map, set,multimap,multiset)，删除当前的iterator，仅仅会使当前的iterator失效**
举例如下： 

```c++
void mapTest(){
    map<int, string> dataMap;
    for (int i = 0; i < 10; i++){
        string strValue = "Hello, World";

        stringstream ss;
        ss << i;
        string tmpStrCount;
    
        ss >> tmpStrCount;
        strValue += tmpStrCount;
    
        dataMap.insert(make_pair(i, strValue));
    }
    cout << "MAP元素内容为：" << endl;
    
    map<int, string>::iterator iter;
    for (iter = dataMap.begin(); iter != dataMap.end(); iter++){
        int nKey = iter->first;
        string strValue = iter->second;
        cout << strValue << endl;
    }
    
    cout << "内容开始删除：" << endl;
    //擦除操作引发迭代器失效
    for (iter = dataMap.begin(); iter != dataMap.end(); iter++){
        int nKey = iter->first;
        string strValue = iter->second;
        if (nKey % 2 == 0){
            dataMap.erase(iter);
        }
        /* cout<<iter->second<<endl;*/
    }
}//报错：vector iterator not incrementable(不可递增)
```

 解决方法：
在erase时，返回值为void，所以我们需要递增当前iterator。
比如： 

```c++
m.erase(iter++)；//先把iter传值到erase里面，然后iter自增，然后执行erase。
```

```c++
for (iter = dataMap.begin(); iter != dataMap.end(); iter++){
    int nKey = iter->first;
    string strValue = iter->second;
    if (nKey % 2 == 0)
    {
        dataMap.erase(iter++);
    }
    /* cout<<iter->second<<endl;*/
}
```

这是因为map之类的容器，使用了红黑树来实现，虽然删除了一个元素，整棵树也会调整，以符合红黑树或者二叉树的规范，但是单个节点在内存中的地址没有变化，变化的是各节点之间的指向关系。 