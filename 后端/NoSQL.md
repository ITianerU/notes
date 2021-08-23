# NoSQL

## NoSQL的四大分类

- KV键值对

  常用于做缓存,**优点**:查询性能高, **缺点:**数据无结构化

  - Redis
  - Tair
  - Memecache

- 文档数据库
  
  value是结构化的, **优点**: 与关系型数据库相比, 数据要求不严格, 表结构可变, 不需要像关系型数据库那样预先定义表结构, **缺点**, 查询性能不高
  
  - MongoDB
  - CouchDB
  
- 列存储数据库

  用于分布式文件系统, 已列簇式存储, 将同一列的数据存在一起, **优点:**查询速度快, 可扩展性强, **缺点:**功能局限

  - HBase
  - 分布式文件系统

- 图形关系数据库

  社交网络, 推荐系统等, **优点:** 利用图结构算法, **缺点:**很多时候需要对整个图做计算, 才能得出需要的信息, 这种结构不太好做分布式的集群方案

  - Neo4j
  - infoGrid

# Redis

## 概述

高速缓存数据库, 每秒读取11万次, 写入8万次,

可以作为数据库, 缓存, 消息中间件(MQ)

**使用6379作为默认端口号的原因:**  作者喜欢的歌手的在键盘位置上拼写对应的数字键

**Redis是单线程的:**  CPU不是Redis的性能瓶颈, 瓶颈是机器的内存和网络带宽

- **为什么单线程依然速度很快:** 因为多线程需要做上下文切换, 消耗性能, 适合用在操作硬盘的场景中, Redis的操作都在内存中, 内存的速度远高于硬盘, 所以单线程操作效率很高

**Redis使用了多线程:**  Redis从6开始支持了多线程 

## 安装

### windows(不推荐)

- github上下载安装包https://github.com/dmajkic/redis/releases 解压压缩包
- 点击redis-server.exe启动服务
- 点击redis-cli.exe客户端测试连接
  - 使用 ping命令, 返回pong则成功

### linux(推荐)

- github上下载安装包https://github.com/dmajkic/redis/releases

- 使用命令解压安装包 

  ```bash
  tar -zxvf 安装包
  ```

- 进入解压后的文件夹, 安装redis的运行环境

  ```bash
  yum install gcc-c++
  # 查看安装版本
  gcc -v
  ```

- 安装

  ```bash
  # 在解压的文件夹下, 执行make命令安装redis
  make
  ```

- redis的安装目录

  ```bash
  /usr/local/bin
  ```

- 备份并修改配置文件

  ```properties
  # 从解压后的文件夹下, 备份配置文件到/usr/local/bin下
  # 并修改配置文件
  # daemonize no => yes 后台启动
  daemonize yes
  ```

-  启动服务

  ```bash
  redis-server [配置文件]
  ```

- 测试连接

  ```八十\
  redis-cli -p 6379
  ping
  ```

- 关闭服务

  ```bash
  # 在使用redis-cli连接后
  shutdown
  exit
  ```

## 工具

### 测试性能

redis-benchmark

```bash
# 测试, 100个并发,  十万请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

## 基础命令

### 数据库操作

```shell
# 测试链接
ping
# 默认有16个数据库, 配置文件中可以看到配置, 默认使用第0个
# 可以切换数据库
select [index]
# 清空当前数据库
flushdb
# 清空全部数据库
flushall
```

### key相关操作

```bash
# 查看当前使用的数据库的key数量
dbsize
# 查看全部key
keys *
# 判断是否存在
exists [key]
# 移动key到指定的数据库
# 如果 key 在目标数据库中已存在，或者 key 在源数据库中不存，则key 不会被移动
move [key] [数据库的索引]
# 设置过期时间
expire [key] [second]
# 查看过期时间
ttl [key]
# 查看类型
type [key]
```

### 赋值

```bash
# 设置值   可选参数 过期时间
set [key] [value] [ex second]
# 设置值的同时, 指定过期时间
setex [key] [second] [value]
# key不存在的时候才会创建, 不会覆盖
setnx [key] [value]
# 批量设置值
mset [k1] [v1] [k2] [v2]
# key不存在的时候才会创建, 不会覆盖
# 是原子操作, 有一个值设置失败, 其他都会设置失败
msetnx [k1] [v1] [k2] [v2]
```

###  取值

```bash
# 获取值
get [key]
# 批量获取
mget [k1] [k2]
# 先获取值, 再设置值
getset [key] [value]
```



## 数据类型

### 基本数据类型

#### String

```bash
# 指定key末尾添加文本吗
# 如果不存在, 就相当于 set key
append [key] [text]
# 值的长度
strlen [key]
# 获取指定范围 0到-1 表示全部
getrange [key] [start_index] [end_index]
# 替换指定位置的字符串, 会根据str的长度, 自动覆盖
setrange [key] [start_index] [str]
# 模拟对象
# 通过设置key的结构, 来模拟对象
set [key:prop] [value]
mset [key:prop] [value]
get [key:prop]
```

#### Number(特殊的String类型)

```bash
# 自增1
incr [key]
# 自减
decr [key]
# 加
incrby [key] [num]
# 减
decrby [key] [num]
```

#### List

大部分命令以 L 开头, 是链表

```bash
# 批量设置值 | 开头批量填加值
lpush [key] [value] [value]
# 批量设置值 | 末尾批量填加值
rpush [key] [value] [value]
# 替换指定位置的值, 指定的位置必须要有值
lset [key] [index] [value]
# 往指定值的前或后插入值
linsert [key] [before|after] [targetValue] [value]
# 获取值 end_index为-1时表示最后一个位置
lrange [key] [start_index] [end_index]
# 移除开头n个元素, 默认1个
lpop [key] [n]
# 移除末尾n个元素, 默认1个
rpop [key] [n]
# 移除n个指定的值
lrem [key] [n] [value]
# 截断, 保留的部分
ltrim [key] [start_index] [end_index]
# 获取指定的值
lindex [key] [index]
# key弹出末尾元素并添加到key2开头
rpoplpush [key] [key2]
# 获取长度
llen [key]
```

#### Set

大部分命令以 S 开头, 值是无需的不会重复

```bash
# 批量添加值
sadd [key] [value] [value]
# 获取全部元素
smembers [key]
# 随机获取指定数量的元素
srandmember [key] [count]
# set中值的数量
scard [key]
# 批量移除指定的元素
srem [key] [value] [value]
# 随机批量删除元素
spop [key] [count]
# 判断是否存在某个元素
sismember [key] [value]
# 移动set中一个元素到另一个set中
# key2不存在会自动创建
smove [key1] [key2] [value] 
# 求两个set的差集, 只会得到key1中key2没有的元素
sdiff [key1] [key2]
# 求两个set的交集
sinter [key1] [key2]
# 求两个set的并集
sunion [key1] [key2]
```

#### ZSet

大部为命令以Z开头, 带顺序的Set

```bash
# 批量添加值, 会通过score的值的大小自动排序
zadd [key] [score] [value] [score] [value]
# 获取值, 通过值的索引位置, 可选参数 [withscores], 获取score
zrange [kev] [start_index] [end_index] [withscores]
# 获取值, 通过值的大小 -inf表示无穷小, inf表示无穷大, 可选参数 [withscores], 获取score
zrangebyscore [kev] [min_score] [max_score] [withscores]
# 倒序 索引从大到小
zrevrange [kev] [start_index] [end_index] [withscores]
# 倒序 值从大到小
zrevrangebyscore [kev] [min_score] [max_score] [withscores]
# 移除指定值
zrem [key] [value]
# 数据数量
zcard [key]
# 在指定大小的区间内获取数量
zcount [key] [min_score] [max_score]
```

#### Hash

大部分命令以 H 开头, 值为键值对

```bash
# 批量设置值
hset [key] [[field] [value]] [[field] [value]]
# 如果不存在就创建
hsetnx [key] [[field] [value]]
# 取值
hget [key] [field]
# 批量获取
hmget [key] [field] [field]
# 获取全部
hgetall hash
# 仅获取全部键
hkeys [key]
# 仅获取全部值
hvals [key]
# 删除
hdel [key] [field]
# 获取键值对数量
hlen [key]
# 判断键是否存在
hexists [key] [field]
# 指定值增加
hincrby [key] [field] [number]
```

### 特殊类型

#### Geospatial

存储地理坐标, 命令以GEO开头, 可以推算两个个位置的距离,

两极无法添加 经度 -180 到 +180; 纬度 -85.05112878到85.051128   

**低层使用zset实现, 可以使用zset的命令**

```bash
# 批量添加
geoadd [key] [经度] [纬度] [name] [经度] [纬度] [name]
# 批量获取指定的经纬度
geopos [key] [name] [name]
# 计算两地的距离
# 单位 m km mi(英里) ft(英尺)
geodist [key] [name] [name] [单位]
# 以给定的经纬度为中心, 找出某一半径内的全部数据
# 可选参数 
# withcoord 获取经纬度
# withdist 获取直线距离
# count num 获取指定的数量
# asc 距离正序 desc 距离倒叙
georadius [key] [经度] [纬度] [radius] [单位] [withcoord] [withdist] [count] [num]
# 以给定的名称为中心, 找出某一半径内的全部数据
georadiusbymember [key] [经度] [纬度] [radius] [单位] [withcoord] [withdist] [count] [num]
# 查看全部
zrange [key] [start_index] [end_index] [withscores]
# 删除
zrem [key] [name]
```

#### Hyperloglog

用于做基数统计, 基数是一个集合中不重复的元素的个数, 可能存在误差 0.81%错误率;

优化了传统方式通过set方式存储, 再计数的方式,

**优点:** 占用内存极小, 固定占用12kb

```bash
# 批量添加
pfadd [key] [value] [value]
# 计算多个集合一起的基数
pfcount [key] [key]
# 合并多个集合到一个新的集合
pfmerge [new_key] [key] [key]
```

#### Bitmaps

位存储, 操作二进制位进行存储

```bash
# 存储, 再index位设置 0 或 1
setbit [key] [index] [0 | 1]
# 获取值
getbit [key] [index]
# 获取值为1的数量
bitcount [key]
```

## 事务

Redis事务的本质: 一组命令的集合! 一个事务中的所有命令都会被序列化, 在事务的执行过程中, 会按照顺序执行!

具有一次性, 顺序性, 排他性

Redis无法保证原子性, 单条命令可以保证原子性, 但是事务无法保证

- 因为再redis事务中, 有一条命令在运行时存错, 不会影响后续的命令运行

Redis没有隔离性(没有隔离级别), 一组set后, 最后收到执行命令才会真的执行

```
set->set->set->执行
```

### 执行过程

如果在执行过程中,出现代码(命令)语法错误, 队列中的命令都不会执行

- 开启事务

```bash
multi
```

- 命令入队

  执行操作命令

- 执行事务

```bash
# 批量执行命令
exec
```

- 放弃事务

```bash
# 放弃事务, 会让事务队列中的命令全部放弃执行
discard
```

### 悲观锁

认为什么时候都会出问题, 每次都会加锁, 性能差

### 乐观锁

认为什么时候都不会出现问题, 不会加锁, 更新数据的时候判断在此期间, 判断数据是否被修改过

#### 步骤

- 获取版本
- 更新时比较版本

```bash
# 监视(加锁), 先获取值
watch [key]
# 开启事务
multi
# 写入命令
set [key] [value]
# 执行命令
# 会比较watch获取的值和当前的值是否相同, 如果修改过,取消事务中的命令, 返回nil
exec
# 如果失败, 需要先取消监视, 再重新监视
# 如果执行过exec或者discard, 不需要主动执行unwatch
unwatch
```

## JAVA中间件

### Jedis

Java连接开发工具

#### 依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.6.3</version>
</dependency>
```

#### 使用

##### 连接

```java
Jedis jedis = new Jedis("url", port);
// 验证密码
jedis.auth("password");
// 连接测试
jedis.ping()
```

##### 常用API

```bash
# 与redis命令相同
```

### SpringData

集成在spirngboot项目中的操作数据库的一个中间件

SpringData-redis 低层使用了lettuce替换了jedis

**jedis:** 采用直连, 多个线程操作不安全, 如果想要避免不安全, 使用jedis pool连接池, BIO模式

**letturce:** 采用netty, 实例可以在多个线程中共享, 不存在线程不安全的情况, 可以减少线程数量, NIO模式, 性能更高

#### 源码

```properties
# 在spring-boot-autoconfiguration包中, META-INF文件夹下, 打开spring.factories文件
# 找到org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration这个类
# 该配置类注解@EnableConfigurationProperties({RedisProperties.class}), 指定了配置项
# 并创建了两个bean, RedisTemplate和StringRedisTemplate
# RedisTemplate更通用
# StringRedisTemplate特指, String类型
```

#### 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

#### 配置

```yaml
spring:
  redis:
    host: localhost
    port: 6379
    password: 123456
```

#### 配置类

```java
```



#### API

```java
// 操作字符串
redisTemplate.opsForValue().命令;
// 操作特殊类型 BitMaps;
stringRedisTemplate.opsForValue().命令;
// 常用操作
redisTemplate.keys();
redisTemplate.exec();
redisTemplate.watch();
// 连接对象
RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
connection.select();
connection.flushdb();
connection.close();
```

