### STL组件之allocator

#### 1. allocator 基本介绍

分配器(allocator))是C ++标准库的一个组件, 主要用来处理所有给定容器(vector，list，map等)内存的分配和释放。C ++标准库提供了默认使用的通用分配器std::allocator，但是，也可以由程序员自己提供自定义分配器。

#### 2. allocator 标准库规范

我们去看std中的stl分配器实现，会发现无论你的实现思路怎么变，所有模板类中的接口和成员变量都是一样的，那么这是因为C++标准库在制定分配器时，是有提出硬性标准的，具体接口和成员变量如下：

STD 标准规范(GNU ISO C++ Library 5.2.1)：

```c++
typedef size_t     size_type; 
typedef ptrdiff_t  difference_type;
typedef _Tp*       pointer; 
typedef const_Tp* const_pointer;
typedef _Tp&       reference;
typedef const_Tp& const_reference;
typedef _Tp        value_type;

void construct(pointer __p, const_Tp& __val) {
    ::new((void *)__p) value_type(__val);
}
void destroy(pointer __p) {
    __p->~_Tp();
}
size_type max_size() const _GLIBCXX_USE_NOEXCEPT {
    return size_t(-1) / sizeof(_Tp);
}
address(const_reference __x) const _GLIBCXX_NOEXCEPT

deallocate(pointer, size_type);

allocate(size_type __n, const void* = 0);

template<typename _Tp1>
struct rebind { 
    typedef allocator<_Tp1> other;
};
```

对于上面这一段接口和变量，其中一些在其他容器的分析中，我们会经常看到，下面对于其中有疑问的点我们来解释一下：

size_type：

为 unsigned 类型 , 表示容器中元素长度或者下标，例：vector::size_type i = 0；我们都知道 size_t 在32位和64位系统上是不一样的，size_t已经可以解决平台差异了，那为什么还要引入size_type，这里我们可以理解为size_t是属于全局的，而size_type是跟容器相关的，是属于STL的一套，在其他容器中也是一样的。

difference_type：

为 signed 类型 , 表示迭代器差距， vector:: difference_type = iter1-iter2，ptrdiff_t通常用来保存两个指针减法操作的结果。

size_t(-1)：

size_t 为 unsigned 类型，传入 -1 获得size_t该系统的最大值。

#### 3. GNU ISO C++ Library 中STL分配器实现

1. 默认分配器：

按照标准规范，标准库中stl实现的分配器，对外接口，成员变量几乎都一样，只是接口内部实现有区别，那么我们看一下具体实现流程， 首先看一下容器默认的分配器std::allocator<_Tp>。 看代码我们会发现，默认分配器真正的实现是在new_allocator.h头文件中做的，头文件具体调用顺序为：allocator.h -> c++allocator.h -> new_allocator.h, 看代码我们知道，在前面两个头文件中基本没做什么，真正的实现为模板类new_allocator。 关键代码如下所示：

```c++
namespace __gnu_cxx _GLIBCXX_VISIBILITY(default){
_GLIBCXX_BEGIN_NAMESPACE_VERSION
  template<typename _Tp>
    class new_allocator{
    public:
      typedef _Tp*       pointer;
      typedef const _Tp* const_pointer;

      // NB: __n is permitted to be 0.  The C++ standard says nothing
      // about what the return value is when __n == 0.
      pointer
      allocate(size_type __n, const void* = 0){ 
          if (__n > this->max_size())
              std::__throw_bad_alloc();
          return static_cast<_Tp*>(::operator new(__n * sizeof(_Tp)));
      }
      // __p is not permitted to be a null pointer.
      void
      deallocate(pointer __p, size_type)
      { ::operator delete(__p); }
    }
    _GLIBCXX_END_NAMESPACE_VERSION
} // namespace
```

我们看一下分配和释放的接口allocate和deallocate，实现上只是单纯的将::operator new和::operator delete进行了一下封装，没用做特殊处理。

当然，如果我们不特别说明，直接在Linux系统下使用标准C++的stl容器，在为容器元素分配内存时，使用的方式就是类似于正常的new和delete，这种方式对于不频繁分配，并且一次分配大块内存的使用情况是适用的，典型的容器像vector和deque。

2. 拓展分配器：

我们继续查看STL源码会发现，除了默认分配器，gnu下的STL还实现了一些特殊的分配器，像__pool_alloc，__mt_alloc，array_allocator，malloc_allocator (在源码的/c++/ext/ 目录下)

__pool_alloc ：比较出名的SGI内存池分配器，侯捷老师在STL源码剖析中着重分析的，后面会专门加一章来进行讲解

__mt_alloc ： 多线程内存池分配器，具体可以看一下该博主的链接讲解，还是比较详细的

array_allocator ： 全局内存分配，只分配不释放，交给系统来释放

malloc_allocator ： 封装了一下std::malloc和std::free

#### 4. 自定义分配器 (allocator)

我们为什么要自定义分配器，废话，主要原因当然是因为性能了。利用自定义分配器可以显著改善程序性能及分配器使用便利性。

对于默认分配器，也就是我们常使用的new和malloc，如果遇到需要频繁分配小块内存对象的情况，因为需要不停的向系统锁要内存，那么将使得分配过程变得很慢；还有就是导致出现过多的内存碎片；以及一些极端情况，分配的内存过于小且数量庞大，导致分配一次内存，附带的内存消耗反而更大，造成内存的浪费

主流提高性能的方式是创建基于内存池的分配器，每次在容器中插入删除元素，不是分配内存，而是分配程序启动时预先分配的大块内存（内存池）。这种自定义分配器，通过简单的从池中返回指向内存的指针来提供单独的分配请求。还有就是可以推迟实践的内存释放，直到内存池生命周期结束

C++之父 Bjarne stroustrup 提到，自定义分配器的三个主要应用，即内存池分配器，共享内存分配器和垃圾收集(gc)分配器

垃圾回收应该就是智能指针，自己控制资源的回收；内存池则主要负责对性能的提升；共享内存分配器则是对特殊缓存区的处理。

传送门： https://www.cnblogs.com/blog-yejy/p/9576586.html 