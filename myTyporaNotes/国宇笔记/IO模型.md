## **五种网络IO模型：**

- **同步阻塞IO**：当用户线程调用请求（如调用read(),write(),listen()等接口），内核就会等待数据的到来，数据到来时实行数据拷贝，然而在内核等待数据到来和实行数据拷贝这段时间用户线程就会被阻塞，直到数据到达线程是阻塞才解除。
- **同步非阻塞IO**:默认创建的socket都是阻塞的，同步非阻塞是在同步阻塞的基础上，将socket设置为NONBLOCK，这个是用ioctl()系统调用设置。用户线程发起调用请求后可以立即返回，如果发起请求后并未读到数据，用户线程可以不断的发起请求，数据到达后，直到读到数据，继续执行。在IO请求的过程中，虽然用户线程每次发起IO请求后可以立即返回，但为了等到数据需要不断的轮询、重复请求，消耗大量的CPU的资源。因此一般很少用此模型。
- **IO多路复用**:IO多路复用模型是建立在内核提供的**多路分离函数select**基础之上的，使用select函数可以避免同步非阻塞IO模型中轮询等待问题，还有poll、epoll都是这种模型。在该模式下，用户首先将需要进行IO操作的socket添加到select中，然后阻塞等待select系统调用返回。当数据到来时，socket被激活，select函数返回。用户线程正式发起read请求，读取数据并继续返回。虽然从流程上看select函数进行IO请求和同步阻塞模型没有太大区别，甚至还添加了监视socket的操作，效率更差，但使用select以后最大的优势是用户可以在一个线程内同时处理多个socket的IO请求。而在同步阻塞中，必须通过多线程的方式才能达到这个目的。
- **信号驱动IO**:调用sigaltion系统调用。当内核中IO数据就绪时以SIGIO信号通知请求进程，请求进程再把数据从内核读入到用户空间，这一步是阻塞的。
- **异步IO**:在该模型中，当用户线程收到通知时，数据已经被内核读取完毕，并放在了用户线程指定的缓冲区内，内核在IO完成后通知用户线程直接使用即可。“真正”异步IO需要操作系统更强的支持。
	信号驱动和异步IO并不十分常用。我们一般使用IO多路复用模拟异步IO的方式。

**首先我们从他们所占有的平台来看**

**select** 是跨平台的 windows、unix、linux下都有
**poll**在linux、unix下有，windows下没有
**epoll** 只有linux特有，unix和windows下没有

**其次从函数原型来看**

**一. select多路复用**
select（）函数允许进程指示内核等待多个事件中（文件描述符）的任何一个发生，并只在有一个或多个事件发生或经历一段指定时间后才唤醒它，然后接下来判断究竟是哪个文件描述符发生了事件并进行相应的处理。
**函数原型及说明**

```c++
struct timeval{
    long tv_sec;     //seconds
    long tv_usec;   //microseconds
 };
FD_ZERO(fd_set *fds)      //清空集合
FD_SET(int fd,fd_set *fds)     //将给定的描述符加入集合
FD_ISSET(int fd,fd_set *fds)   //判断指定描述符是否在集合中
FD_CLR(int fd,fd_set *fds)     //将给定的描述符从文件中删除
    
//File descriptor    fd		文件描述符
```

 int select（int max_fd , fd_set * readset , fd_set * writeset , fd_set * exceptset , struct timeval * timeout）;
说明：select监视的并等待多个文件描述符的属性发生变化，监视的属性分为3类，分别是readfds(文件描述符有数据到来可读)，writefds（文件描述符可写），exceptfds(文件描述符异常)。调用select函数会阻塞，直到有文件描述符就绪（有数据可读、可写、错误异常），或者超时（timeout 指定等待时间）发生函数才返回。当select()函数返回后，可以通过遍历fdset,来找到究竟是哪些文件描述符就绪。

1. select函数的返回值是就绪描述符的数目，超时时返回0，出错返回-1；
2. max_fd指待测试的fd的总个数，它的值是待测试的最大文件描述符加1（linux内核是从0开始到max_fd-1扫描文件描述符）。
3. 中间三个参数readset、writeset和exceptset指定要让内核测试读、写和异常条件的fd集合，如果不需要测试的可以设置为NULL；
4. 最后一个参数是设置select的超时时间，如果设置为NULL则永不超时。 

**二. poll多路复用**

poll函数的原型及说明

```c++
struct poll{ 
    int    fd;
    short  events;
    short revents;
 };
 int  poll(struct  pollfd *fds , nfds_t   nfds,int  timeout);
```

说明：
**第一个参数**：用来指向一个struct pollfd类型的一个数组，结构体的events域是用户传给内核的，revents域是内核返还给用户的。
**第二个参数**：nfds指定数组中监听的元素个数。
**第三个参数**：timeout指定等待的毫秒数，无论I/O是否准备好，poll都会返回。timeout指定为负数值表示永不超时，使poll()一直挂起直到一个指定事件发生；timeout为0指示poll调用立即返回并列出准备好I/O的文件描述符，但并不等待其他事件的发生。

**三. epoll多路复用**

epoll的设计和实现与select完全不同。epoll通过在linux内核中申请一个简易的文件系统，把原先的select/poll调用分成3个部分：
1.调用epoll_create()建立一个epoll对象

```c++
int epoll_create(int size);
```

若创建成功返回文件描述符，若失败返回-1。参数size指定了我们想要通过epoll实例来检查的文件描述符个数，该参数并不是一个上限，而是告诉内核应该如何为内部数据结构划分初始大小。

2.调用epoll_ctl向epoll对象中添加这100万个连接的套接字

```c++
int epoll_ctl(int  epfd,int op,int fd,struct epoll_event *ev);
```

系统调用epoll_ctl()能够修改由文件描述符epfd所代表的epoll实例中的兴趣列表。若成功返回0，若出错返回-1。
**第一个参数**：epfd是epoll_create()的返回值。

**第二个参数op**：用来指定需要执行的操作，它的值可以是如下几种值：

**EPOLL_CTL_ADD**：将描述符fd添加到epoll实例中的兴趣列表中去。对于fd上我们感兴趣的事件，都指定在ev所指向的结构体中。如果我们试图向兴趣列表中添加一个已存在的文件描述符，epoll_ctl（）将出现EEXIST错误。
**EPOLL_CTL_MOD**:修改描述符上设定的事件。需要用到由ev所指向的结构体中的信息。
**EPOLL_CTL_DEL**:将文件描述符fd从epfd的兴趣列表中移除，该操作忽略参数ev。

**第三个参数fd**:指明了要修改兴趣列表中的哪一个文件描述符的设定。

**第四个参数ev**：指向结构体epoll_event的指针。
结构体定义如下：

```c++
typedef union epoll_data{
    void   *ptr;
    int      fd;
    uint32_t   u32;
    uint64_t   u64;
};

struct epoll_event{
    uint32_t   events;
    epoll_data_t  data;
};
```

参数ev为文件描述符fd所做的设置如下：
events字段是一个位掩码，他指定了我们为待检查的描述符fd上所感兴趣的事件的集合；

data字段是一个联合体，当描述符fd稍后称为就绪态，联合的成员可用来指定传回给调用进程的信息；

3.调用epoll_wait收集发生的事件的连接

```c++
int epoll_wait(int epfd,struct epoll_event  * evlist , int  maxevent , int  timeout);
```

系统调用epoll_wait()返回实例中处于就绪态的文件描述符信息，单个epoll_wait()调用能够返回多个就绪态文件描述符的信息。调用成功后epoll_wait()返回数组evlist中的元素个数。如果在timeout超时间隔内没有任何文件描述符处于就绪态的话就返回0，出错时返回-1。
**第一个参数epfd**:epoll_create（）的返回值。

**第二个参数evlist**：所指向的结构体数组中返回的是有关就绪态文件描述符的信息，数组evlist的空间由调用者负责申请。

**第三个参数maxevents**：指定evlist数组中所包含的元素个数。

**第四个参数timeout**：用来确定epoll_wait（）的阻塞行为，由如下几种：

- 如果timeout等于-1，调用将一直阻塞，直到兴趣列表中的文件描述符上有事件产生或直到捕获到一个信号为止。
- 如果timeout等于0，执行一次非阻塞式的检查，看兴趣列表中的文件描述符上产生了哪个事件。
- 如果timeout大于0，调用将阻塞至多timeout毫秒，直到文件描述符上有事件发生，或者直到捕获到一个信号为止。

**最后从各自的优缺点来看**

**select** 的缺点：
它不但对所监视的文件描述符有数量上的限制（虽然数量可以修改，但也会造成效率的降低），而且每次访问一个文件描述符都要遍历所有的文件描述符。内核/用户空间存在拷贝问题，select需要复制大量的句柄数据结构，这对系统产生了巨大的开销。select的触发方式是水平触发，应用程序如果没有完成对一个已经就绪的文件描述符进行I/O操作，那么之后每次select调用还是会将这些文件描述符通知进程。

**poll**：由于使用链表保存文件描述符，所以没有监视文件描述符数量上的限制，但其他缺点依然存在。

**epoll**：它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。在获取事件的时候无需遍历所有被监听的文件描述符集，只需要遍历被内核IO事件异步唤醒而加入就绪队列的文件描述符集就行。它还采用两种触发模式边沿触发和水平触发。