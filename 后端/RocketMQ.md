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

一号机

```bash
mkdir /usr/local/rocketmq/store/broker-a
mkdir /usr/local/rocketmq/store/broker-a/commitlog
mkdir /usr/local/rocketmq/store/broker-a/consumequeue
mkdir /usr/local/rocketmq/store/broker-a/index
mkdir /usr/local/rocketmq/store/broker-b-s
mkdir /usr/local/rocketmq/store/broker-b-s/commitlog
mkdir /usr/local/rocketmq/store/broker-b-s/consumequeue
mkdir /usr/local/rocketmq/store/broker-b-s/index
```

二号机

```bash
mkdir /usr/local/rocketmq/store/broker-b
mkdir /usr/local/rocketmq/store/broker-b/commitlog
mkdir /usr/local/rocketmq/store/broker-b/consumequeue
mkdir /usr/local/rocketmq/store/broker-b/index
mkdir /usr/local/rocketmq/store/broker-a-s
mkdir /usr/local/rocketmq/store/broker-a-s/commitlog
mkdir /usr/local/rocketmq/store/broker-a-s/consumequeue
mkdir /usr/local/rocketmq/store/broker-a-s/index
```

### 配置文件

配置双主双从同步

一号机 目录 /conf/2m-2s-sync

```properties
~ broker-a.properties  配置master

#暴露的外网IP
brokerIP1=1号服务器公网IP
brokerIP2=1号服务器公网IP
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=0
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=10911
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmq/store/broker-a
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/broker-a/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/broker-a/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/broker-a/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/broker-a/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmq/store/broker-a/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SYNC_MASTER
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```

```properties
~ broker-b-s.properties  配置slave

#暴露的外网IP
brokerIP1=1号服务器公网IP
brokerIP2=1号服务器公网IP
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-b
#0 表示 Master，>0 表示 Slave
brokerId=1
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=11011
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmq/store/broker-b-s
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/broker-b-s/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/broker-b-s/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/broker-b-s/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/broker-b-s/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmq/store/broker-b-s/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SLAVE
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```

2号机

```properties
~ broker-b.properties  配置master
#暴露的外网IP
brokerIP1=2号服务器公网IP
brokerIP2=2号服务器公网IP
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-b
#0 表示 Master，>0 表示 Slave
brokerId=0
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=10911
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmq/store/broker-b
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/broker-b/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/broker-b/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/broker-b/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/broker-b/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmq/store/broker-b/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SYNC_MASTER
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```

```properties
#暴露的外网IP
brokerIP1=2号服务器公网IP
brokerIP2=2号服务器公网IP
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=1
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=11011
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmq/store/broker-a-s
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/broker-a-s/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/broker-a-s/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/broker-a-s/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/broker-a-s/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmq/store/broker-a-s/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SLAVE
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```

### 修改启动脚本

```bash
vim /usr/local/rocketmq/bin/runbroker.sh
vim /usr/local/rocketmq/bin/runserver.sh
```

修改启动时占用内存

```properties
# 开发环境配置 JVM Configuration
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"
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

## 监控平台

### 安装

```bash
docker pull styletang/rocketmq-console-ng
```

### 启动

```bash
docker run -e "JAVA_OPTS=-Drocketmq.namesrv.addr=[namersrv地址]:9876;[namersrv地址]:9876; -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8080:8080 -t styletang/rocketmq-console-ng
```

# 项目

## 搭建

### 添加客户端依赖

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>4.4.0</version>
</dependency>
```

## 生产者

### 消息发送者步骤分析

1. 创建消息生产者producer, 并制定生产者组名
2. 指定nameserver地址
3. 启动produrcer
4. 创建消息对象, 指定Topic主题, Tag和消息体
5. 发送消息
6. 关闭生产者producer

## 消费者

### 消息消费者步骤分析

1. 创建消费者consumer, 指定消费者组名
2. 指定nameserver地址
3. 订阅主题Topic和Tag
4. 设置回调函数, 处理消息
5. 启动消费者

## 基本样例

### 消息发送

#### 发送同步消息

可靠性高, 适用于重要的消息通知

```java
public static void main(String[] args) throws Exception {
    // 创建消息生产者producer, 并制定生产者组名
    DefaultMQProducer producer = new DefaultMQProducer("group1");
    // 指定nameserver地址
    producer.setNamesrvAddr("172.17.0.2:9876;172.168.0.3:9876");
    // 启动produrcer
    producer.start();
    for (int i=0; i<10; i++){
        // 创建消息对象, 指定Topic主题, Tag和消息体
        Message msg = new Message("base", "tag1", ("hello world"+(i+1)).getBytes());
        // 发送消息, 并接受返回的发送状态
        SendResult result = producer.send(msg);
        System.out.println("发送结果"+result);
    }
    // 关闭生产者producer
    producer.shutdown();
}
```

#### 发送异步消息

可靠性低, 适用于对响应时间敏感的场景

```java
public class AsyncProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("172.17.0.2:9876;172.168.0.3:9876");
        producer.start();
        for (int i=0; i<10; i++){
            Message msg = new Message("base", "tag2", ("hello world"+(i+1)).getBytes());
            // 异步通过回调函数获取响应结果
            producer.send(msg, new SendCallback() {
                // 发送成功
                public void onSuccess(SendResult sendResult) {
                    System.out.println("发送结果:" + sendResult);
                }
                // 发送失败
                public void onException(Throwable throwable) {
                    System.out.println("发送异常:" + throwable);
                }
            });
        }
        // 立刻shutdown可能会导致回调函数, 没有等待到响应结果报异常 
        // Thread.sleep(2000);
        producer.shutdown();
    }
}

```

#### 发送单向消息

不接受响应, 适用于不关心发送结果的场景

```java
public class OnewayProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("172.17.0.2:9876;172.168.0.3:9876");
        producer.start();
        for (int i=0; i<10; i++){
            Message msg = new Message("base", "tag1", ("hello world"+(i+1)).getBytes());
            producer.sendOneway(msg);
        }
        producer.shutdown();
    }
}
```

### 消息接收

启动后会一直监听订阅的消息

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        // Push为推的模式
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        consumer.setNamesrvAddr("172.17.0.2:9876;172.17.0.3:9876");
        // 订阅消息
        consumer.subscribe("base", "tag1");
        // 订阅tag1 和tag2
        //consumer.subscribe("base", "tag1 || tage2");
        // 订阅t全部
        //consumer.subscribe("base", "*");
        // 设置监听器
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            // 回调, 获取订阅的数据
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, 												ConsumeConcurrentlyContext consumeConcurrentlyContext) {
                for (MessageExt msg: list) {
                    System.out.println(new String(msg.getBody()));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动
        consumer.start();

    }
}
```

#### 广播模式

多个消费者, 接收的mq内容相同

```java
consumer.setMessageModel(MessageModel.BROADCASTING);
```

#### 负载均衡模式(默认)

多个消费者, 均分mq消息

## 顺序消息

broker中可能有多个队列,  生产者发送多条消息, broker以轮询的方式将消息分给不同的队列, 

消费者在消费时, 使用多线程的方式去消费, 默认无法保证多个队列的消息是有序的,

为保证消息的顺序, 需要将消息全部放到一个队列中

实体

```java
public class OrderStep {
    private long orderId;
    private String desc;

    public long getOrderId() {
        return orderId;
    }

    public void setOrderId(long orderId) {
        this.orderId = orderId;
    }

    public String getDesc() {
        return desc;
    }

    public void setDesc(String desc) {
        this.desc = desc;
    }

    @Override
    public String toString() {
        return "OrderStep{" +
                "orderId=" + orderId +
                ", desc='" + desc + '\'' +
                '}';
    }

    public static List<OrderStep> buildOrders(){
        List<OrderStep> orderList = new ArrayList<OrderStep>();

        OrderStep orderDemo = new OrderStep();
        orderDemo.setOrderId(1039L);
        orderDemo.setDesc("创建");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(1065L);
        orderDemo.setDesc("创建");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(1039L);
        orderDemo.setDesc("付款");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(7235L);
        orderDemo.setDesc("创建");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(1065L);
        orderDemo.setDesc("付款");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(7235L);
        orderDemo.setDesc("付款");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(1065L);
        orderDemo.setDesc("完成");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(1039L);
        orderDemo.setDesc("推送");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(7235L);
        orderDemo.setDesc("完成");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(1039L);
        orderDemo.setDesc("完成");
        orderList.add(orderDemo);

        return orderList;
    }

}
```



### 消息发送

```java
public class producer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("172.17.0.2:9876;172.168.0.3:9876");
        producer.start();
        List<OrderStep> orderSteps = OrderStep.buildOrders();
        int i = 0;
        for (OrderStep order: orderSteps) {
            Message msg = new Message("OrderTopic", "order", ++i+"",(order+"").getBytes());
            /*
            *  参数
            *  消息队列选择器
            *  选择队列的业务标识
            * */
            SendResult sendResult = producer.send(msg, new MessageQueueSelector() {
                // 选择队列
                public MessageQueue select(List<MessageQueue> list, Message message, Object o) {
                    Long orderId =  (Long) o;
                    long index = orderId % list.size();
                    return list.get((int) index);
                }
            }, order.getOrderId());
            System.out.println("发送结果:" + sendResult);
            Thread.sleep(1000);
        }

        producer.shutdown();
    }
}
```

### 消息接收

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        // Push为推的模式
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        consumer.setNamesrvAddr("172.17.0.2:9876;172.17.0.3:9876");
        // 订阅消息
        consumer.subscribe("OrderTopic", "*");
        // 设置监听器
        // 使用MessageListenerOrderly, 在消费时, 会对一个队列, 使用一个线程去处理
        consumer.registerMessageListener(new MessageListenerOrderly() {
            public ConsumeOrderlyStatus consumeMessage(List<MessageExt> list, ConsumeOrderlyContext consumeOrderlyContext) {
                for (MessageExt msg: list
                     ) {
                    System.out.println("线程名称: " + Thread.currentThread().getName() + "消费消息: " + new String(msg.getBody()));
                }
                return ConsumeOrderlyStatus.SUCCESS;
            }
        });
        // 启动
        consumer.start();
        System.out.println("消费者启动");
    }
}
```





























