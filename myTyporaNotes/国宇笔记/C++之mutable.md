 mutable的中文意思是“可变的，易变的”，跟constant（既C++中的const）是反义词。

　　在C++中，mutable也是为了突破const的限制而设置的。被mutable修饰的变量，将永远处于可变的状态，即使在一个const函数中。

　　我们知道，如果类的成员函数不会改变对象的状态，那么这个成员函数一般会声明成const的。但是，有些时候，我们需要在const的函数里面修改一些跟类状态无关的数据成员，那么这个数据成员就应该被mutalbe来修饰。 

```c++
class ClxTest{
　public:
　　ClxTest();
　　~ClxTest();

　　void Output() const;
　　int GetOutputTimes() const;

　private:
　　mutable int m_iTimes;
};

ClxTest::ClxTest(){
　m_iTimes = 0;
}

ClxTest::~ClxTest()
{}

void ClxTest::Output() const{
　cout << "Output for test!" << endl;
　m_iTimes++;
}

int ClxTest::GetOutputTimes() const{
　return m_iTimes;
}

void OutputTest(const ClxTest& lx){
　cout << lx.GetOutputTimes() << endl;
　lx.Output();
　cout << lx.GetOutputTimes() << endl;
}
```

 计数器m_iTimes被mutable修饰，那么它就可以突破const的限制，在被const修饰的函数里面也能被修改。 