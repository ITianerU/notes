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