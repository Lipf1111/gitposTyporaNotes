# OSI七层参考模型图

![在这里插入图片描述](TCP、IP协议栈.assets/2019032111013066.png)

![img](TCP、IP协议栈.assets/705728-20160424234824085-667046040.png)

![img](TCP、IP协议栈.assets/705728-20160424234826351-1957282396.png)

![img](TCP、IP协议栈.assets/705728-20160424234827195-1493107425.png)

# 1 应用层协议

## 1.1 DHCP协议

- DHCP是使用UDP的应用层协议

### 图示

![img](TCP、IP协议栈.assets/clipboard-1603336658444.png)

- <font color=blue>四种状态</font>

![img](TCP、IP协议栈.assets/clipboard-1603336700593.png)

# 2 传输层协议

## 2.1 TCP协议

- 传输控制协议

### TCP协议特性

1. 工作在传输层。
2. 面向连接的
3. 通信传输协议采用全双工协议
4. 可靠性传输
5. 拥塞控制，慢起动和拥塞避免算法
6. 错误检查
7. 将数据打包成段，排序
8. 确认机制
9. 流量控制，滑动窗口
10. 半关闭
11. 数据恢复与重传

### TCP包头结构

![img](TCP、IP协议栈.assets/clipboard.png)

## 2.2 UDP协议

- 用户数据报协议

### UDP协议特性

1. 工作在传输层
2. 面向无连接
3. 提供不可靠的网络访问
4. 传输效率高
5. 有限的错误检查
6. 无数据恢复特性

### UDP包头

![img](TCP、IP协议栈.assets/clipboard-1603336515446.png)

# 3 网络层

## 3.1 ARP协议

- **<font color=red>ARP协议在TCP/IP模型中位于网络层，OSI模型中位于链路层</font>**
- 地址解析协议，通过解析IP地址得到MAC地址

## 3.2 IP协议

- 负责把一个数据包从一个网段转发到另一个网段

### IP协议报头

![img](TCP、IP协议栈.assets/clipboard-1603336599604.png)

## 3.3 ICMP协议

- 负责检测网络层是否有故障，<font color=red>ping命令就来自ICMP协议</font>

## 3.4 IGMP协议

- 用来报告多址广播组成员身份的协议
- 通知本地路由器希望加入或接受某个特定组播组的信息
- 路由器通过IGMP协议周期性查询某个局域网内某个已知组成员是否处于活动状态

















