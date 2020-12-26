```c++
#include <iostream>
using namespace std;

template <class T>
class Myarray {
public:
	//设置数组容量
	int m_Capacity;
	//设置数组内已存元素个数
	int m_Size;
	//设置数据首地址
	T* m_pAddr;
public:
    //有参构造
	Myarray(int capacity) {
		this->m_Capacity = capacity;
		this->m_Size = 0;
		this->m_pAddr = new T[this->m_Capacity];
	};
	//拷贝构造
	Myarray(const Myarray<T>& array) {
		this->m_Capacity = array.m_Capacity;
		this->m_Size = array.m_Size;
		this->m_pAddr = new T[array.m_Capacity];
		for (int i = 0; i < array.m_Size; i++)
			this->m_pAddr[i] = array.m_pAddr[i];
	};
	//向数组尾部插入元素
	void pushBack(T &value) {
		//如果元素已经满了，扩容
		if (this->m_Size == this->m_Capacity) {
			this->m_Capacity = 2*this->m_Capacity;
			T *m_pNewAddr = new T[this->m_Capacity];
			for (int i = 0; i < this->m_Size; i++) {
				m_pNewAddr[i] = this->m_pAddr[i];
			}
            delete[] this->m_pAddr;
			this->m_pAddr = m_pNewAddr;
		}
		this->m_pAddr[this->m_Size++] = value;
	};
	//重载[]号方便取值
	T& operator[](int index) {
		return this->m_pAddr[index];
	};
	//赋值构造
	Myarray<T>& operator=(const Myarray<T>& array) {
		if (this->m_pAddr != NULL) {
			delete[] this->m_pAddr;
			this->m_pAddr = NULL;
		}
		this->m_Capacity = array.m_Capacity;
		this->m_Size = array.m_Size;
		this->m_pAddr = new T[array.m_Capacity];
		for (int i = 0; i < array.m_Size; i++) {
			this->m_pAddr[i] = array.m_pAddr[i];
		}
		return *this;
	};
	~Myarray() {
		if (this->m_pAddr != NULL) {
			delete[] this->m_pAddr;
			this->m_pAddr = NULL;
		}
	}
};

class Person {
public:
	Person(int a, int b) {
		this->a = a;
		this->b = b;
	}
	int a = 9;
	int b = 10;
};

int main() {
	Myarray<int> a(2);
	int c = 50, d = 60;
	a.pushBack(c);
	a.pushBack(d);
	for (int i = 0; i < a.m_Size; i++) {
		cout << a[i] << " ";
	}
	cout << endl;

	Myarray<char> bb(4);
	char z = 'a';
	char x = 'd';
	bb.pushBack(z);
	bb.pushBack(x);
	for (int i = 0; i < bb.m_Size; i++) {
		cout << bb[i] << " ";

	}
	cout << endl;

	Myarray<int> cc(20);
	Myarray<int> dd(20);
	dd = cc = a;
	for (int i = 0; i < cc.m_Size; i++) {
		cout << cc[i] << " ";
	}
	cout << endl;
	for (int i = 0; i < dd.m_Size; i++) {
		cout << dd[i] << " ";

	}
	cout << endl;
    
	Myarray<Person> pp(1);
	Person p1(2, 3),p2(4,5);
	pp.pushBack(p1);
	pp.pushBack(p2);
	for (int i = 0; i < pp.m_Size; i++) {
		cout << "a = " << pp[i].a << "  b = " << pp[i].b << " ";
	}
	cout << endl;
};
```

```c+
	Myarray<int> a(2);
	a.pushBack(10);
	a.pushBack(20);
	a.pushBack(30);
	//如果想直接以引用方式插入数字（右值），需要这样传值
void pushBack(T&& value) {
	//如果元素已经满了，扩容
	if (this->m_Size == this->m_Capacity) {
		this->m_Capacity = 2 * this->m_Capacity;
		T* m_pNewAddr = new T[this->m_Capacity];
		for (int i = 0; i < this->m_Size; i++) {
			m_pNewAddr[i] = this->m_pAddr[i];
		}
		this->m_pAddr = m_pNewAddr;
	}
	this->m_pAddr[this->m_Size++] = value;
}
```

