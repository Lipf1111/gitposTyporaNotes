###  数据分发服务DDS 

(Data Distribution Service)是对象管理组织(OMG)在HLA及CORBA等标准的基础上制定的新一代==分布式实时通信中间件技术规范==，DDS采用==发布/订阅==体系架构，强调以数据为中心，提供丰富的QoS服务质量策略，能保障数据进行实时、高效、灵活地分发，可满足各种分布式实时通信应用需求。DDS信息分发中间件是一种轻便的、能够提供实时信息传送的中间件技术。 

#### 技术特点

- 灵活的发布/订阅模式
- 完整DDS规范QoS服务质量策略
- 已扩展的QoS服务质量策略
- 互操作
- 强实时
-  跨平台
- 支持多种底层物理通信协议
- 仿真→测试→实装的全生命周期支持

#### DDS做了什么？

​	使得系统层和应用程序层分开，使得数据的分发和管理，既不用动系统底层，也不用开发人员去为了提高性能在应用程序层绞尽脑汁，提供了java的原生接口，其他语言可以使用代码托管，跨语言调用DDS的接口

#### DDS发布订阅模型

​	DDS以数据为中心的发布一订阅模型为所有分布式节点之间建立了一个虚拟共享的“全局数据空间(Global Data Space, GDS)。

​	在该模型下分布式节点在网络上以发布或订阅的方式传输数据，节点可以是发布者或订阅者，或者既是发布者又是订阅者。

​	网络中的数据对象用主题((Topic)做标识，分布式节点在全局数据空间中发布或订阅感兴趣的主题信息。

​	各个节点在逻辑上无主从关系，点与点之间都是对等关系.通信方式可以是点对点、点对多、多对多等，在QoS的控制下建立连接，自动发现和配置网络参数。

###  简介

   DDS是基于发布/订阅模式的通信模型。发布/订阅中间件提供一种简单、直观的方式分发数据，它将创建和发送数据（数据发布者（Publisher））的软件与接收和使用数据（数据订阅者（Subscriber））的软件分离开。Publihser简单声明其发送意图并发布数据。Subscriber声明其接收意图，然后中间件自动传送数据

### 实现过程

应用程序使用API建立实体（对象），以此建立彼此间的发布/订阅通信。需要建立的主要实体包含：
– 域（Domain）
– 域参与者（DomainParticipant）
– 数据写入者（DataWriter）
– 发布者（Publisher）
– 数据读取者（DataReader）
– 订阅者（Subscriber）

– 主题（Topic）       

![img](.\Pic\70.jpg)

​    发送方使用的对象被称为Publisher和DataWriter，接收方使用的对象被称为Subscriber和DataReader。

- 应用程序使用DataWriter发送数据。DataWriter与单独主题相关联，用户可以在单独应用程序中拥有多个DataWriter和主题。另外，在单独应用程序中，用户可以为一个特定的主题拥有超过一个DataWriter。
- Publisher是一个负责实际数据发送的DCPS对象。Publisher拥有和管理DataWriter。一个DataWriter仅可由一个Publisher拥有，而一个Publisher可以拥有多个DataWriter，因此，相同的Publisher可以为不同数据类型的很多不同主题发送数据，当用户代码在DataWriter上调用writer()方法时，数据样本被传送至网络上执行数据实际分发的Publisher对象。
- 应用程序使用DataReader访问DCPS上接收的数据。一个DataReader关联一个单独主题，用户可以在一个应用程序中拥有多个DataReader和主题。另外，在单独的应用程序中，用户可以为一个特定的主题拥有超过一个DataReader。
- Subscriber是负责发布数据实际接收的DCPS对象。Subscriber拥有和管理DataReader。一个DataReader只能由一个单独Subscriber拥有，而一个Subscriber可以拥有很多DataReader。因此，相同的Subscriber可以为不同数据类型的很多不同主题接收数据。当数据被发送至应用程序时，它首先由Subscriber处理，数据样本随后被储存在适当的DataReader中。用户代码注册一个可在新数据到达时调用的监听器，或使用read()和take()方法积极轮询DataReader的新数据。

### RTI DDS主要接口

#### DDS初始化，创建域参与者

```c++
DDS_ReturnCode_t rti_dds_init(intdomainId);
```

RTI 运行支撑环境 Run-Time Infrastructure

#### 数据写入者初始化

```c++
DDS_StringDataWriter* rti_dds_create_datawriter(const char *topicName, const struct DDS_DataWriterListener* listener = NULL);
```

#### 数据读取者初始化

```c++
DDS_StringDataReader * rti_dds_create_datareader(const char *topicName, const struct DDS_DataReaderListener* listener = NULL);
```

#### 字符流数据读取者句柄获取

```c++
DDS_StringDataReader * rti_dds_stringdatareader_narrow(DDS_DataReader *);
```

#### 数据发布

```c++
DDS_ReturnCode_t rti_dds_stringdata_publish(DDS_StringDataWriter* self, const char*instance_data);
```

#### 数据订阅

```c++
DDS_ReturnCode_t rti_dds_stringdata_subscribe(DDS_StringDataReader*self, char* instance_data);
```

#### DDS域参与者实体释放

```c++
DDS_ReturnCode_t rti_dds_dispose();
```

### VC工程配置

打开VC工程属性->配置属性。

#### C/C++ ->常规->附加包含目录：

Ø $(NDDSHOME)\include

Ø $(NDDSHOME)\include\ndds

#### C/C++ ->预处理器->预处理器定义：

##### Debug模式：

Ø WIN32

Ø RTI_WIN32

Ø _DEBUG

Ø _CONSOLE

##### Release模式：

Ø WIN32

Ø RTI_WIN32

Ø _CONSOLE

#### 链接器->常规->附加库目录：

Ø $(NDDSHOME)\lib\i86Win32VS2010

#### 链接器->输入->附加依赖项：

##### Debug模式：

Ø nddsczd.lib

Ø nddscorezd.lib

Ø netapi32.lib

Ø advapi32.lib

Ø user32.lib

Ø WS2_32.lib

##### Release模式：

Ø nddscz.lib

Ø nddscorez.lib

Ø netapi32.lib

Ø advapi32.lib

Ø user32.lib

Ø WS2_32.lib

#### 链接器->输入->忽略特定默认库

libcmtd

#### 应用案例

- 洛克希德马丁公司Aegis军械系统
- 波音公司B-1B轰炸机军械系统
- 波音公司B-1B及B52数据链系统
- 波音公司扫描鹰无人机侦查及地面控制系统
- 捕食者及空中勇士无人机地面数字方舱系统
- 德国RoboScout机器人侦查车内部通讯系统
- 美国DDG1000全舰计算系统
- 大众汽车辅助驾驶和安全集成系统
- NASA智能机器人系统
- CAE飞行模拟器系统
- 欧洲空中交通管理系统