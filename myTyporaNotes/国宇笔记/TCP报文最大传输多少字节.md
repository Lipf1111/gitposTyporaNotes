### 一个TCP报文最多传输多少字

#### 答案

一个TCP报文最多传输多少字节(即MSS)？
答案：1440

#### 分析

报文头的长度
TCP: 70 = 18+20+32
UDP: 46 = 18+20+8

其中链路层：18,IP层:20, TCP:32 UDP:8，如下图：(各协议的报文详情见附录)
![img](.\Pic\753880-20200601145852772-825154055.png)

但我们通常说得**MTU(最大传输单元)**并不包括链路层，那么：
TCP: 52 = 20+32
UDP: 28 = 20+8

有些想象力丰富的小朋友会发现,链路层的协议上写着数据段大小是46-1500会不会是弄错了？
答：其实这个46和UDP没有一毛钱关系。因为链路层传输字段的最小长度是64,链路协议占了18,64-18=46。也就是说，不管你发不发数据，这个46的长度是少不了的。

MTU的长度：
以太网限制长度：1500
IEEE 802.3要求长度：1492

取较小值，也就是真正用来传输数据的长度：
1440 = 1492 - 52

所以我们经常抓包的时候会发现，MSS(最大报文段长度)有时会是1440这个数。当然经过复杂的网络环境MSS会更小。

#### 附录

![img](.\Pic\753880-20200601145907632-1367509655.jpg)
![img](.\Pic\753880-20200601145921475-2027629242.jpg)
![img](.\Pic\753880-20200601145942808-1090114392.jpg)
![img](.\Pic\753880-20200601145955187-2032757830.jpg)