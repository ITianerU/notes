# MQ介绍

消息队列是一种先进先出的数据结构

## 优点

- **应用解耦**

  系统耦合度越高， 容错率越低， MQ可降低耦合度

  A系统发MQ给B系统， B系统挂掉不会影响到A系统

- **流量削峰**

  流量激增时， 可能会将数据库压跨， 使用MQ将用户请求缓存， 系统慢慢处理

- **数据分发**

  可将数据分发给多个系统

  A系统将数据给MQ， 其他系统想要获取数据， 只要订阅MQ即可， 不需要修改A系统代码

## 缺点

- 系统可用性降低

- 系统复杂度提高

  系统由原来的同步调用， 变为异步调用， 需解决MQ可能被重复消费，可能丢失，不同系统对MQ处理的顺序可能不一致

- 一致性问题

  多系统对MQ数据处理可能有的成功， 有的失败

## 各类MQ比较

|    特性    |        ActiveMQ         |                   RabbitMQ                    |       RocketMQ       |                 kafka                 |
| :--------: | :---------------------: | :-------------------------------------------: | :------------------: | :-----------------------------------: |
|  开发语言  |          java           |                    erlang                     |         java         |                 scala                 |
| 单机吞吐量 |          万级           |                     万级                      |        10万级        |                10万级                 |
|   时效性   |          ms级           |                     us级                      |         ms级         |               ms级以内                |
|   可用性   |     高（主从架构）      |                高（主从架构）                 | 非常高（分布式架构） |         非常高（分布式架构）          |
|  功能特性  | 较多文档， 协议支持出色 | 并发能力强，性能极其好，延时低， 管理界面丰富 | 功能完善， 扩展性佳  | 只支持主要的MQ功能， 主要为大数据准备 |

# 安装

这里使用docker安装最新版本

- docker search RocketMQ
- docker pull [下载的版本]
- docker run -it -name RocketMQ -P [镜像名]

# 目录

- benchmark   运行demo
- bin （常用）可执行文件
- conf   配置文件
- lib    依赖jar包

# 启动/停止

## **nameserver**

进入bin目录下

```cmd
# 启动
nohup sh mqnamesrv &
# 停止
sh mqshutdown namesrv
```

## **broker**

进入bin目录下

```cmd
# 启动
nohup sh mqbroker -n localhost:9876&
# 停止
sh mqshutdown mqbroker
```

**注**

RocketMQ默认分配的内存比较大， 如果broker无法正常启动， 可能是内存不足

```cmd
# 修改下面启动文件， 分配内存大小
vi runbroker.sh
vi runserver.sh
```

## **测试**

### 发送消息

```cmd
export NAMESRV_ADDR=localhost:9876
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
```

### 接收消息

```cmd
export NAMESRV_ADDR=localhost:9876
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```

# 日志

日志创建在root目录下

```cmd
tail -f /root/logs/rocketmqlogs/namesrv.log 
```

# 角色

- Producer: 消息提供者
- Consumer:  消息消费者
- Broker:  暂存和传输消息
- NameServer:  管理Broker, 类似注册中心
- Topic:  区分消息种类; 
- Message Queue:  相当于Topic分区(子分类),  用于并行发送和接收消息

# 集群

![集群](../图片/RocketMQ/集群.png)

Producer提供消息, 先向Name Server询问, 发送给哪个Broker

Consumer消费消息, 也要先向Name Server询问, 接收哪个Broker的消息

## 特点

- Name Server, Producer, Consumer 是无状态的,  broker会给每个Name Server上报信息,  每个Name Server的数据都相同, 不需要数据交互,

  此特点, 让Name Server, Producer, Consumer  启动多个, 即可搭建集群

- Broker 分为Master, Slave, Master处理写(接收)操作,  Slave处理读(提供)操作

  Name Server 把Broker Name相同的节点当作一组,  通过Broker ID区分,  0为Master, 非0为Slave

  一个Master可有多个Slave,  一个Slave只能有一个Master

  Master会通过同步/异步, 将数据发送给Slave

- Producer与Name Server集群其中一个建立长连接,  每次发送消息都要询问Name Server,  要发送给哪个broker,  Name Server通过携带的Topic来判断

  Producer也会与Master建立长连接,  定时向Master发送心跳检测,  判断Producer状态

- Consumer与Name Server集群其中一个建立长连接,  定期询问Name Server,

  Consumer也会与Master, Slave建立长连接,  定时向Master, Slave发送心跳检测,  判断Consumer状态

  Consumer接收数据有两种方式,  一种是broker主动推送,  一种是consumer主动接收

  Consumer既可订阅Master的消息,  也可订阅Slave的消息

## 集群模式

- **单Master模式**(非集群)

  风险大,  一旦Broker重启或者宕机, 会导致整个服务不可用

- **多Master模式**

  无Slave,  全是Master

  -  **优点**

    配置简单,  单个Master宕机或重启对应用无影响,  性能最高

  - **缺点**

    单台机器宕机期间,  这台机器上未被消费的消息在机器回复之前, 不可订阅, 消息实时形受到影响

- **多Master模式多Slave模式(异步)**

  Producer向Broker发送消息后, Broker直接返回响应

  - **优点**

    磁盘损坏, 消息丢失最少, 消息实时性不受影响, Master宕机, 消费者仍然可以从Slave消费, 性能同多Master模式一样

  - **缺点**

    Master宕机, 磁盘损坏会丢失少量消息

- **多Master模式多Slave模式(同步)**

  Producer向Broker发送消息后, Broker的Master先将数据同步到Slave,  再做响应

  - **优点**

    数据与服务都无单点故障, Master宕机情况下,  消息无延迟,  服务可用性与数据可用性非常高

  - **缺点**

    性能比异步复制模式略低, 大约10%, 发送单个消息RT略高

## 集群工作流程

1. 启动Name Server,  监听端口,  等待Broker, Producer, Consumer连接

2. Broker启动后, 与Name Server保持长连接, 定时发送心跳包.  心跳包包含当前Broker信息, 以及存储所有的Topic信息.

   注册成功后, NameServer中就有Topic和Broker的映射关系

3. 发送消息前, 先创建Topic, 创建Topic需要指定该Topic存储在哪些Broker上,  也可以在发送消息时自动创建Topic

4. Producer发送消息, 启动时先跟NameServer集群中的一台建立长连接, 并从NameServier中获取当前发送的Topic存在哪些Broker上, 轮询从队列列表中选择一个队列, 然后与队列所在的Broker建立长连接从而向Broker发消息.

5. Consumer跟Producer类似, 跟NameServer集群中的一台建立长连接,从NameServier中获取订阅的Topic存在哪些Broker上, 与Broker建立通道, 消费消息

## 安装

### 配置Host

```properties
# nameserver
172.17.0.4  rocketmq-nameserver1
172.17.0.5  rocketmq-nameserver2
# broker
172.17.0.4  rockermq-master1
172.17.0.4  rockermq-slave2
172.17.0.5  rockermq-master2
172.17.0.5  rockermq-slave1
```

### 开放端口

使用docker, 只要开启宿主机防火墙 

```cmd
# 开放端口
firewall-cmd --zone=public --add-port=[端口号]/tcp --permanent
# 重启防火墙
firewall-cmd --reload
```

### 创建消息存储路径

```bash
mkdir /usr/local/rocketmq/store
mkdir /usr/local/rocketmq/store/commitlog
mkdir /usr/local/rocketmq/store/consumequeue
mkdir /usr/local/rocketmq/store/index
```

### 配置文件

配置双主双从同步

目录 /conf/2m-2s-sync

```properties
~ broker-a.properties  配置master

# 集群名字,  集群每个节点的集群名字都相同
brokerClusterName=rocketmq-cluster   
# broker 名字
brokerName=broker-a
# broker id  0表示Master, >0 表示Slave
brokerId=0      
# nameServer地址
namesrvAddr=172.17.0.4:9876;172.17.0.5:9876
# 在发送消息时, 自动创建服务器不存在的topic,  默认创建的队列数
defaultTopicQueueNums=4
# 是否允许 Broker 自动创建Topic, 建议线下开启, 线上关闭
autoCreateTopicEnable=true
# 是否允许 Broker 自动创建订阅组, 建议线下开启, 线上关闭
autoCreateSubscriptionGroup=true
# Broker 对外服务监听的端口, 在同一台机器上启动主从broker端口不能重复
listenPort=10911
# 删除无用消息的时间   04表示凌晨4点
deleteWhen=04  
# 刷盘时间(文件保留时间) 单位/小时
fileReservedTime=120 
# commitLog 每个文件大小
mapedFileSizeCommitLog=1073741824
# ConsumeQueue每个文件,存储条数
mapedFileSizeConsumeQueue=300000
# commitlog目录所在分区的最大使用比例，如果commitlog目录所在的分区使用比例大于该值，则触发过期文件删除
diskMaxUsedSpaceRatio=88
# 存储路径
storePathRootDir=/usr/local/rocketmq/store
# commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/commitlog
# 消费队列存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/consumequeue
# 消息索引存放路径
storePathIndex=/usr/local/rocketmq/store/index
# checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/checkpoint
# abort 文件存储路径
abortFile=/usr/local/rocketmq/store/abort
# 限制消息大小
maxMessageSize=65536
# broker 角色   ASYNC_MASTER 异步复制Master, SYNC_MASTER 同步双写, SLAVE 从
brokerRole=SYNC_MASTER
# 刷盘策略  SYNC_FLUSH  同步刷,  ASYNC_FLUSH  异步刷
flushDiskType=SYNC_FLUSH      

```

```properties
~ broker-b-s.properties  配置slave

# 集群名字,  集群每个节点的集群名字都相同
brokerClusterName=rocketmq-cluster   
# broker 名字
brokerName=broker-b
# broker id  0表示Master, >0 表示Slave
brokerId=1    
# nameServer地址
namesrvAddr=172.17.0.4:9876;172.17.0.5:9876
# 在发送消息时, 自动创建服务器不存在的topic,  默认创建的队列数
defaultTopicQueueNums=4
# 是否允许 Broker 自动创建Topic, 建议线下开启, 线上关闭
autoCreateTopicEnable=true
# 是否允许 Broker 自动创建订阅组, 建议线下开启, 线上关闭
autoCreateSubscriptionGroup=true
# Broker 对外服务监听的端口
listenPort=11011
# 删除无用消息的时间   04表示凌晨4点
deleteWhen=04  
# 刷盘时间(文件保留时间) 单位/小时
fileReservedTime=120 
# commitLog 每个文件大小
mapedFileSizeCommitLog=1073741824
# ConsumeQueue每个文件,存储条数
mapedFileSizeConsumeQueue=300000
# commitlog目录所在分区的最大使用比例，如果commitlog目录所在的分区使用比例大于该值，则触发过期文件删除
diskMaxUsedSpaceRatio=88
# 存储路径
storePathRootDir=/usr/local/rocketmq/store
# commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/commitlog
# 消费队列存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/consumequeue
# 消息索引存放路径
storePathIndex=/usr/local/rocketmq/store/index
# checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/checkpoint
# abort 文件存储路径
abortFile=/usr/local/rocketmq/store/abort
# 限制消息大小
maxMessageSize=65536
# broker 角色   ASYNC_MASTER 异步复制Master, SYNC_MASTER 同步双写, SLAVE 从
brokerRole=SLAVE
# 刷盘策略  SYNC_FLUSH  同步刷,  ASYNC_FLUSH  异步刷
flushDiskType=SYNC_FLUSH      
```

### 启动

**nameserver启动** 

分别启动每个服务器的nameserver

```bash
nohup sh mqnamesrv &
```

**broker启动**

分别启动每个broker的主从节点

```
nohup sh mqbroker -c [broker-a.properties路径] &
```





















































