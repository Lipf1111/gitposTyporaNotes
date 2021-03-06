SYN	Synchronize	同步

ACK	Acknowledgement	确认 

FIN	Finish	完成

seq	sequence number	序列号

URG	urgentpointer	紧急指针

MSS	 Maximum Segment Size 	最大报文段

 PSH	接收方应该尽快将这个报文交给应用层。 

 ![20180829092121169ae1ab-761a-4861-8a9e-832f840ae973.png](.\Pic\20180829092121169ae1ab-761a-4861-8a9e-832f840ae973.png) 

#### 三次握手过程

1. 最开始客户端和服务器，都处于 `CLOSED` 状态，二者之间没有任何联系；
2. 客户端向服务器发起连接请求，具体操作是发送一个 SYN 给服务器，客户端状态转而变为 `SYN-SENT` ,**这是第一次握手** ；
	- 客户端发送位码SYN=1,随机产生seq number=1234567的数据包到服务器。
3. 由于客户端向服务器发送请求了，服务器被动的进入 `LISTEN` 状态；
	- 服务器端收到请求，查看SYN=1,即知道客户端要求建立联机。
4. 如果服务器没有想要客户端的请求，则客户端得不到服务器的回应，请求就会超时，一旦请求超时，客户端的状态，就会再次进入 `CLOSED` ；
5. 如果服务器响应了这个请求，具体操作是：接收到了客户端发送的 SYN，同时向客户端发送回应 SYN+ACK，表示我收到你的请求了；然后服务器的状态，进入 `SYN-RECEIVED` ; **这是第二次握手** ；
	- 服务器端收到请求联机请求信息后，向客户端发送ACK number=(客户端的seq+1),SYN=1,ACK=1,产生随机seq number=7654321的包到客户端。
6. 客户端收到服务器的回应 SYN+ACK，然后再次向服务器发送一个 ACK，表示我收到你的回应了 ；客户端的状态进入 `ESTABLISHED` ；**这是第三次握手** ；
	- 客户端检查检查ack number是否正确，即是否是第一次发送的seq+1,检查位码ACK是否为1，若正确，客户端在发送ack number=(服务器seq+1)，ACK=1。服务器端收到后，检查seq值及ACK值，正确则完成第三次握手。
7. 服务器收到客户端的响应 ACK 以后，状态进入 `ESTABLISHED` ；
8. 至此，三次握手完成，客户端与服务器建立了可靠的连接，可以进行数据传输了 ；

 ![img](.\Pic\13141414.jpg)

 ![1600597003582](.\Pic\1600597003582.png)

![1600597085889](.\Pic\1600597085889.png)

#### 四次挥手过程

1. 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。
2. 第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。
3. 第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。
4. 第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。 

上面是**有一方先于对方，发起关闭请求** ,下面说下，双方同时发起关闭请求的情况；

当双方同时发起关闭请求的时候，双方在发送完FIN 以后，都进入 `FIN-WAIT-1` 状态；**然后如果双发又同时收到对方的FIN，以及同时收到对方回应的ACK**，则直接进入 `TIME-WAIT` ；
如果双方同时收到FIN，**但是没有同时收到ACK**，则先进入 `CLOSING`，然后，在双方都收到 ACK 以后，再进入 `TIME-WAIT` ;

为什么在进入 `TIME-WAIT` 的状态以后，会等待 2MSL 的时间，再进入 `CLOSED`？

MSL 翻译为：报文最大生存时间 ；,2MSL则是两个报文最大生存世时间，等待这个时间的原因：是因为在网络不好的时候，有时候，需要重新发送报文，因此进入这里的等待下 ；

 ![img](.\Pic\124124.jpg)



