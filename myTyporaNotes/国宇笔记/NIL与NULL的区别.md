NIL与NULL的区别

一、概念不同

Nil：表示无值，任何变量在没有被赋值之前的值都为nil。

Null：是在计算中具有保留的值。

二、功能不同

Nil：用于区别其他任何值。

Null：用于指示指针不引用有效对象。

三、针对不同

Nil：针对对象，而空对象不是说不占用空间，相当于一个“洗白”，回到初始状态。

Null：针对指针，对对象指针和非对象指针都有效，Null不会占用空间。