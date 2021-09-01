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
# 登录
auth [password]
# 测试链接
ping
# 默认有16个数据库, 配置文件中可以看到配置, 默认使用第0个
# 可以切换数据库
select [index]
# 清空当前数据库
flushdb
# 清空全部数据库
flushall
# 查看当前库信息
info replication
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

不自定义配置类, 设置的key前面可能出现转义字符

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        // JSON序列化配置
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入是非final类
        om.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
        jackson2JsonRedisSerializer.setObjectMapper(om);

        // String序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        // key使用String序列化配置
        template.setKeySerializer(stringRedisSerializer);
        // hash的key使用String序列化配置
        template.setHashKeySerializer(stringRedisSerializer);
        // 值使用JSON序列化配置
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // 值使用JSON序列化配置
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        // 将redis配置的的参数设置进去
        template.afterPropertiesSet();
        return template;
    }
}
```

#### API

实体类必须要序列化, 并且要有无参构造器

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

#### 工具类

```java
package com.itianeru.redisdemo.redisdemo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import java.util.Collection;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@Component
public final class RedisUtil {

    @Autowired(required = false)
    private RedisTemplate<String, Object> redisTemplate;

    /**
     * 指定缓存失效时间
     * @param key 键
     * @param time 时间(秒)
     * @return boolean 成功/失败
     */
    public boolean expire(String key, long time){
        try {
            if (time > 0){
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     * @param key 键
     * @return long 时间, 返回0表示永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }

    /**
     * 判断key是否存在
     * @param key 键
     * @return boolean 存在/不存在
     */
    public boolean hasKey(String key){
        try {
            return redisTemplate.hasKey(key);
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除缓存
     * @param key 键, 可传多个
     * @return void
     */
    public void del(String... key){
        if (key != null && key.length > 0){
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete((Collection<String>) CollectionUtils.arrayToList(key));
            }
        }
    }

    // -------------------------------------  String -----------------------------------------------

    /**
     * 普通缓存获取
     * @param key 键
     * @return java.lang.Object 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key 键
     * @param value 值
     * @return boolean 成功/失败
     */
    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 普通缓存放入, 并设置过期时间
     * @param key 键
     * @param value 值
     * @param time 时间(秒) time<=0 将设置无限期
     * @return boolean 成功/失败
     */
    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0){
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 增加(整数)
     * @param key 键
     * @param delta 要增加的值
     * @return long 增加之后的数
     */
    public long incr(String key, long delta) {
        if (delta < 0){
            throw new RuntimeException("要增加的值必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }

    /**
     * 增加(浮点数)
     * @param key 键
     * @param delta 要增加的值
     * @return double 增加之后的数
     */
    public double incr(String key, double delta) {
        if (delta < 0){
            throw new RuntimeException("要增加的值必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 减少(整数)
     * @param key 键
     * @param delta 要减少的值
     * @return long 减少之后的数
     */
    public long decr(String key, long delta) {
        if (delta < 0){
            throw new RuntimeException("要减少的值必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }

    /**
     * 减少(浮点数)
     * @param key 键
     * @param delta 要减少的值
     * @return double
     */
    public double decr(String key, double delta) {
        if (delta < 0){
            throw new RuntimeException("要减少的值必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }

    // ------------------------------------ Map ------------------------------------------------

    /**
     * 获取hash中某一项的值
     * @param key 键
     * @param item 项
     * @return java.lang.Object 值
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }
    
    /**
     * 获取hash所有键值
     * @param key 键 
     * @return java.util.Map<java.lang.Object,java.lang.Object> Map 键值对
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * 存入hash
     * @param key 键
     * @param map 多个键值
     * @return boolean 成功/失败
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 存入hash, 并指定过期时间
     * @param key 键
     * @param map 多个键值
     * @param time 时间(秒) time<=0 将设置无限期
     * @return boolean 成功/失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time < 0) {
                expire(key, time);
            }
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 设置hash中某一项的值, 如果该项不存在将被创建
     * @param key 键
     * @param item hash中key
     * @param value 值
     * @return boolean 成功/失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 设置hash中某一项的值, 如果该项不存在将被创建, 并指定过期时间
     * @param key 键
     * @param item hash中key
     * @param value 值
     * @param time 时间(秒) time<=0 将设置无限期 注意: 如果已存在的hash有过期时间, 这里会替换原有的过期时间
     * @return boolean 成功/失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除hash表中的值
     * @param key 键
     * @param item 要删除的项, 可以指定多个
     * @return void
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }

    /**
     * 判断hash表中是否有该项的值
     * @param key 键
     * @param item 项
     * @return boolean 存在/不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }

    /**
     * 增加hash中某一项(整数), 如果不存在会创建一个
     * @param key 键
     * @param item 项
     * @param delta 要增加的值
     * @return long 增加后的数
     */
    public long hincr(String key, String item, long delta) {
        return redisTemplate.opsForHash().increment(key, item, delta);
    }

    /**
     * 减少hash中某一项(浮点数), 如果不存在会创建一个
     * @param key 键
     * @param item 项
     * @param delta 要增加的值
     * @return double 增加后的数
     */
    public double hincr(String key, String item, double delta) {
        return redisTemplate.opsForHash().increment(key, item, delta);
    }

    /**
     * 减少hash中某一项(整数), 如果不存在会创建一个
     * @param key 键
     * @param item 项
     * @param delta 要减少的值
     * @return long 减少后的数
     */
    public long hdecr(String key, String item, long delta) {
        return redisTemplate.opsForHash().increment(key, item, -delta);
    }

    /**
     * 减少hash中某一项(浮点数), 如果不存在会创建一个
     * @param key 键
     * @param item 项
     * @param delta 要减少的值
     * @return double 减少后的数
     */
    public double hdecr(String key, String item, double delta) {
        return redisTemplate.opsForHash().increment(key, item, -delta);
    }

    // ----------------------------------- Set -----------------------------------------------

    /**
     * 获取set所有值
     * @param key 键
     * @return java.util.Set<java.lang.Object> Set集合
     */
    public Set<Object> sGet(String key){
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 从指定的set中查询值是否存在
     * @param key 键
     * @param value 值
     * @return boolean 存在/不存在
     */
    public boolean sHasValue(String key, Object value){
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将数据放入set缓存
     * @param key 键
     * @param values 值, 可以多个
     * @return long 成功个数
     */
    public long sSet(String key, Object... values){
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 将数据放入set缓存, 并指定过期时间
     * @param key 键
     * @param time 时间(秒), time<=0 将设置无限期
     * @param values 值, 可以多个
     * @return long 成功个数
     */
    public long sSet(String key, long time, Object... values){
        try {
            long count = redisTemplate.opsForSet().add(key, values);
            if(time > 0){
                expire(key, time);
            }
            return count;
        } catch (Exception e){
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 获取set缓存长度
     * @param key 键
     * @return long set长度
     */
    public long sGetSetSize(String key){
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 移除指定set的中某些值
     * @param key 键
     * @param values 值, 可以多个
     * @return long 移除的个数
     */
    public long setRemove(String key, Object... values){
        try {
            return redisTemplate.opsForSet().remove(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // -------------------------------------------- List --------------------------------------

    /**
     * 获取list中的指定区间的值
     * @param key 键
     * @param start 开始
     * @param end 结束  如果为-1 表示末尾
     * @return java.util.List<java.lang.Object> 获取的值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
    
    /**
     * 获取指定list的长度
     * @param key 键
     * @return long list长度
     */
    public long lGetListSize(String key){
        try {
            return redisTemplate.opsForList().size(key);
        }catch (Exception e){
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 获取指定list中, 指定索引的值
     * @param key 键
     * @param index  索引 index>=0时, 0是第一位, index<0时, -1是末尾, -2倒数第二位
     * @return java.lang.Object 值
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e){
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 将值存入指定list末尾, 指定list不存在, 会创建
     * @param key 键
     * @param value 值
     * @return boolean 成功/失败
     */
    public boolean lSet(String key, Object value){
        try{
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将值存入指定list末尾, 指定list不存在, 会创建, 并指定过期时间
     * @param key 键
     * @param value 值
     * @param time 时间(秒), time<=0 将设置无限期
     * @return boolean 成功/失败
     */
    public boolean lSet(String key, Object value, long time){
        try{
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list的全部值存入指定list末尾, 指定list不存在, 会创建
     * @param key 键
     * @param value 值
     * @return boolean 成功或失败
     */
    public boolean lSet(String key, List<Object> value){
        try{
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list的全部值存入指定list末尾, 指定list不存在, 会创建, 并指定过期时间
     * @param key 键
     * @param value 值
     * @param time 时间(秒), time<=0 将设置无限期
     * @return boolean 成功/失败
     */
    public boolean lSet(String key, List<Object> value, long time){
        try{
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 修改指定list, 指定索引的值
     * @param key 键
     * @param index 索引
     * @param value 替换的值
     * @return boolean 成功/失败
     */
    public boolean lUpdateIndex(String key, long index, Object value){
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 移除指定list中指定数量的目标值
     * @param key 键
     * @param count 要移除数量
     * @param value 目标值
     * @return long 成功移除的个数
     */
    public long lRemove(String key, long count, Object value){
        try {
            return redisTemplate.opsForList().remove(key, count, value);
        } catch (Exception e){
            e.printStackTrace();
            return 0;
        }
    }
}
```

## 配置文件

```properties
################################## 包含 ###################################

# 可以将其他配置文件包含进来
include /path/to/local.conf
include /path/to/other.conf

################################## 网络 ###################################

# 绑定的本机地址吗, 如果要远程访问需要注掉
bind 127.0.0.1 -::1

# 保护模式 如果要远程访问, 需要改为 no
protected-mode yes

# 端口
port 6379

################################## 通用 ###################################

# 是否以守护进程模式开启, 开启为yes
daemonize no

# pid进程文件, 如果守护进程模式运行, 需要指定
pidfile /var/run/redis_6379.pid

# 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
timeout 0

# 日志级别
# 日志从上到下, 越来越少
# verbose 和debug类似
# debug  一般用于测试开发阶段
# notice 一般用于生产环境
# warning
loglevel notice

# 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
logfile ""

# 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
databases 16

# 是否显示logo
always-show-logo yes

################################## 持久化 ##################################
################################# RDB持久化 ###############################

# 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
# save 3600 1      如果3600秒内, 有1个key进行了修改, 就进行持久化操作
# save 300 100     如果300秒内, 有100个key进行了修改, 就进行持久化操作
# save 60 10000    如果60秒内, 有10000个key进行了修改, 就进行持久化操作
save <seconds> <changes>

# 持久化出错后, 是否还要继续工作
stop-writes-on-bgsave-error yes

# 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
rdbcompression yes

# 保存rdb文件时, 是否做检查
rdbchecksum yes

# 指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

# 指定rdb本地数据库存放目录
dir ./

############################## AOF持久化 ###############################

# 默认不开启, 默认使用rdb方式持久化
appendonly no

# 持久化文件
appendfilename "appendonly.aof"

# 每次都要同步的配置
# appendfsync everysec 每秒一次同步, 可能会丢失这1s的数据
# appendfsync always 每一次修改都同步, 消耗性能
# appendfsync no 不执行同步, 操作系统自动调度刷磁盘，性能是最好的
appendfsync everysec

################################# 主从复制 #################################
# 配置主机ip
replicaof <masterip> <masterport>

# 当master服务设置了密码保护时，slav服务连接master的密码
# masterauth <master-password>

replica-serve-stale-data yes

replica-read-only yes

repl-diskless-sync no

repl-diskless-sync-delay 5

repl-diskless-load disabled

repl-disable-tcp-nodelay no

replica-priority 100


################################## 安全 ###################################

# 登录密码
requirepass 123456

################################### 客户端 ####################################

# 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
maxclients 10000

############################## 内存 ################################

# 最大内存设置
maxmemory <bytes>

# 内存满了的处理策略
# volatile-lru：只对设置了过期时间的key进行LRU（默认值） 
# allkeys-lru： 删除lru算法的key   
# volatile-random： 随机删除即将过期key   
# allkeys-random： 随机删除   
# volatile-ttl： 删除即将过期的   
# noeviction： 永不过期，返回错误
maxmemory-policy noeviction
```

## 持久化

- RDB默认的持久化方式, fork一个进程, 如果在一段时间内修改了n条数据, 这个进程就会将内存中的全量数据, 保存到持久化的文件中
  - **优点**: 性能好, 占用的空间小, redis重启服务时, 恢复数据快
  - **缺点**: 如果在没达到触发持久化的条件, redis服务异常, 内存这段时间的新数据会丢失, 当数据量大的时候, 因为是全量替换, 会影响性能 
- AOF, 每次发生写的操作, 都会记录一条操作日志, 通过日志持久化数据
  - **优点:** 数据安全性比RDB要好, 日志文件可读, 可以处理误操作
  - **缺点:** 日志占用空间大, 数据a修改了20次, 就要记录20次, 实际只会用到最后一次, 恢复数据慢, 每次都读写同步, 有性能压力
- 如果同时开启了两种持久化方式, 会优先使用AOF来恢复数据, 因为AOF保存的数据可能会更完整

### RDB

默认使用, 在主从复制中, rdb用于备用在从机上

#### 配置

```properties
# 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
# save 3600 1      如果3600秒内, 有1个key进行了修改, 就进行持久化操作
# save 300 100     如果300秒内, 有100个key进行了修改, 就进行持久化操作
# save 60 10000    如果60秒内, 有10000个key进行了修改, 就进行持久化操作
save <seconds> <changes>

# 持久化出错后, 是否还要继续工作
stop-writes-on-bgsave-error yes

# 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
rdbcompression yes

# 保存rdb文件时, 是否做检查
rdbchecksum yes

# 指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

# 指定rdb本地数据库存放目录
dir ./
```

#### 触发规则

- save规则满足的情况下, 会触发
- flushall命令, 也会触发
- 关闭redis也会触发

#### 恢复数据

- 把dump.rdb放在配置的文件夹下,启动redis即可

### AOF

#### 配置

```properties
# 默认不开启, 默认使用rdb方式持久化
appendonly yes

# 持久化文件
appendfilename "appendonly.aof"

# 每次都要同步的配置
# appendfsync everysec 每秒一次同步, 可能会丢失这1s的数据
# appendfsync always 每一次修改都同步, 消耗性能
# appendfsync no 不执行同步, 操作系统自动调度刷磁盘，性能是最好的
appendfsync everysec


```

#### 触发规则

- 重启即可生效

#### 修复持久化文件

```bash
# 如果appendonly.aof损坏
# redis会报错, 可能无法连接
# 使用修复工具
redis-check-aof --fix [文件名]
```

#### 重写规则

```bash
# 如果该参数设置为no，是最安全的方式，不会丢失数据，但是要忍受阻塞的问题。如果设置为yes呢？这就相当于将appendfsync设置为no，这说明并没有执行磁盘操作，只是写入了缓冲区，因此这样并不会造成阻塞（因为没有竞争磁盘），但是如果这个时候redis挂掉，就会丢失数据。丢失多少数据呢？在linux的操作系统的默认设置下，最多会丢失30s的数据。
# 因此，如果应用系统无法忍受延迟，而可以容忍少量的数据丢失，则设置为yes。如果应用系统无法忍受数据丢失，则设置为no。
no-appendfsync-on-rewrite no
# 如果aof文件大于配置的64M, 会fork一个新的进程来将文件进行重写
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```

## 发布订阅

### 命令

```bash
# 批量订阅频道, 之后会等待推送的信息
subscribe [频道] [频道]
	# 消息内容, 有三条
	message  # 第一条固定为message
	[频道]   # 第二条是订阅的频道
	[msg]   # 第三条, 接受到的内容
# 批量退订频道
unsubscribe [频道] [频道]
# 往指定频道发送消息
publish [频道] [msg]
```

### 原理

redis-server维护了一个字典, 字典的key是频道的名称, value是订阅这个频道的所有客户端组成的链表

发送消息时, 会通过频道名称, 找到客户端链表, 遍历链表,向每个客户端发送消息

### 整合SpringBoot

#### 配置类

```java
@Configuration
public class RedisConfig {
    @Bean
    RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory,
                                            MessageListenerAdapter listenerAdapter1,
                                           MessageListenerAdapter listenerAdapter2) {

        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        // 可以添加多个 messageListener，配置不同的监听器
        container.addMessageListener(listenerAdapter1, new PatternTopic("频道1"));
	    container.addMessageListener(listenerAdapter2, new PatternTopic("频道2"));
        return container;
    }
    /**
     * 消息监听器适配器，绑定消息处理器，利用反射技术调用消息处理器的业务方法
     */
    @Bean
    MessageListenerAdapter listenerAdapter1(RedisReceiver1 redisReceiver1) {
        return new MessageListenerAdapter(redisReceiver1, "receiveMessage");
    }
    @Bean
    MessageListenerAdapter listenerAdapter2(RedisReceiver2 redisReceiver2) {
        return new MessageListenerAdapter(redisReceiver2, "receiveMessage");
    }
}
```

#### 消息处理器

```java
@Component
public class RedisReceiver1 {
    public void receiveMessage(String message) {
        System.out.println(message);
    }
}
@Component
public class RedisReceiver2 {
    public void receiveMessage(String message) {
        System.out.println(message);
    }
}
```

## 主从复制

### 概念

将一台redis服务器的数据, 复制到其他的redis服务器上, 前者称为master节点, 后者称为slave节点

数据复制是单项的, 只能由主节点到从节点, 主节点以写为主, 从节点以读为主

默认情况下, 每台redis服务器都是主节点, 且一个主节点可以有多个从节点, 但是一个从节点只能由一个主节点

主从复制的作用包括

- 数据冗余: 主从复制实现了数据的热备份
- 故障恢复: 当主节点出现问题, 可以有从节点提供服务, 实现快速的故障恢复
- 负载均衡: 在主从复制上, 配合读写分离, 可以由主节点提供写服务, 从节点提供读服务,分担服务器负载
- 高可用: 主从复制还是烧饼和集群能够实施的基础
- 当从机挂掉， 并重连， 主机会将全部数据发送给从机

### 环境配置

#### 查看当前库信息

```bash
info replication
	# 返回的信息
    role:master     # 角色, master主机, slave从机
    connected_slaves:0      # 连接的从机数量
    master_failover_state:no-failover
    master_replid:5ec12f2e4189fd877d4e8ca588e63d8e3b1f7f23
    master_replid2:0000000000000000000000000000000000000000
    master_repl_offset:0
    second_repl_offset:-1
    repl_backlog_active:0
    repl_backlog_size:1048576
    repl_backlog_first_byte_offset:0
    repl_backlog_histlen:0
```

#### 配置文件

```bash
# 创建多个配置文件
# 如果在同一个宿主机上启动多台redis, 需要将pid文件名, 日志文件名, 持久化文件名, 端口号改为不同
# 如果使用docker部署, 只需要改端口的映射
# 如果主机配置了密码， 需要在从机配置主机的密码，  masterauth password
```

#### 命令配置（重启会失效）

```bash
# 使用redis-cli连接客户端后
# 配置从机
slaveof [主机ip] [端口号]

# 手动设置当前节点是主节点
slaveof on one
```

#### 文件配置（一直生效）

```bash
# 配置从机的配置文件
# 配置主机ip
replicaof <masterip> <masterport>

# 当master服务设置了密码保护时，slav服务连接master的密码
# masterauth <master-password>
```

### 启动

```bash
# docker启动
docker run -p 6379:6379 --name redis1 -v D:\env\docker\redis\conf:/usr/local/etc/redis  -d redis  redis-server /usr/local/etc/redis/redis1.conf
docker run -p 6380:6380 --name redis2 -v D:\env\docker\redis\conf:/usr/local/etc/redis  -d redis  redis-server /usr/local/etc/redis/redis2.conf
docker run -p 6381:6381 --name redis3 -v D:\env\docker\redis\conf:/usr/local/etc/redis  -d redis  redis-server /usr/local/etc/redis/redis3.conf
```

### 复制原理

Slave启动成功后， master后会发送一个sync同步命令

Master接到命令， 启动后台的存盘进程， 同时收集所有接收到的用于修改数据集的命令， 在后台进程执行完毕后， master将传送整个数据文件到slave， 并完成一次完全同步（全量复制）

**全量复制** 而slave服务在接受到数据库文件数据后， 将其存盘并加载到内存中

**增量复制** Master继续将新的收集到的修改命令一次传给slave， 完成同步

但是只要是重新连接master， 一次完全同步（全量复制）将会被自动执行

### 其他主从复制模式

#### 链式模式

```bash
# 三个节点
# B节点是A节点的子节点， C节点是B节点的子节点
# A节点set的值， 依然会传递到C节点上
# 此时B节点依然是从节点， 不能进行写入操作
# 如果A节点挂掉， 这个时候可以手动在B节点手动 slaveof no one， 设置B节点为主节点， B节点就可以写入
```

## 哨兵模式（常用）

主节点挂掉后， 从节点自动选举出一个新的主节点；

哨兵模式是一种特殊的模式， 首先Redis提供了哨兵的命令， 哨兵是一个独立的进程， 作为进程， 它会独立运行， 其原理是哨兵通过发送命令， 等待redis服务器的响应， 从而监控运行的多个redis实例

为了防止哨兵也挂掉， 通常也会配置哨兵集群， 假设主机宕机， 哨兵1先检测到这个结果， 系统不会立刻failover（故障转移）， 仅仅是哨兵1主观认为这个服务器不管用， 这个现象称为**主机下线**，当后面的哨兵也检测到主机不可用， 并且数量达到一定的值时， 那么哨兵之间会进行一次投票，选举出新的主机， 投票的结果会通过一个哨兵发起， 进行failover（故障转移）操作， 切换成功后， 就会通过发布订阅模式， 让各个哨兵把自己监控的从服务器实现切换主机， 这个过程称为客观下线

### 哨兵配置

文件名必须为 sentinel.conf

```properties
# 1 表示该哨兵有1次认为该主机挂了， 就从新选举
# 如果设置为2， 表示该哨兵有2次认为该主机挂了， 或者有两个哨兵认为该主机挂了， 就重新选举
sentinel monitor [被监控的主机name] [监控的服务ip] [port] 1
# 如果设置了密码
sentinel auth-pass [被监控的主机name] [password]
# 如果有多个哨兵， 要配置多个端口
port 26379
# 哨兵的工作目录
dir /tmp
# 指定多少毫秒后， 主节点没有应答哨兵， 此时哨兵主观认为该主节点下线， 默认30s
sentinel down-after-milliseconds [被监控的主机name] [毫秒数]
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行同步，
# 这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。
# 可以通过将这个值设为1来保证每次只有一个slave处于不能处理命令请求的状态。
sentinel parallel-syncs [被监控的主机name] 1
#故障转移的超时时间failover-timeout 可以用在以下这些方面:
# 1.同一个sentine1对同一 个master两次fai lover之间的间隔时间。
# 2.当一个slave从一 个错误的master那里同步数据开始计算时间。直到s1ave被纠正为向正确的master那里同步数据时。
# 3.当想要取消一个正在进行的failover所需要的时间。
# 4.当进行failover时，配置所有s1aves指向新的master所需的最大时间。不过，即使过了这个超时，slaves 依然会被正确配置为指向master,但是就不按parallel-syncs所配置的规则来了
# 默认三分钟 180000
# sentine1 failover-timeout <master-name> <milliseconds>
sentine1 fai lover-ti meout [被监控的主机name] [毫秒数]

# SCRIPTS EXECUTION
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则:
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被-一个SIGKILL信号终止，之后重新执行。

#通知型脚本:当sentine1有任何警告级别的事件发生时(比如说redis实例的主观失效和客观失效等等)，将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等 方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一 个是事件的类型，一个是事件的描述。如果sentine1. conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentine1无法正常启动成功。
#通知脚本
# she11编程
# sentine1 notification-script <master-name> <script-path>
sentine1 notificati on-script mymaster /var/redis/notify. sh

#客户端重新配置主节点参数脚本
#当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
#以下参数将会在调用脚本时传给脚本: 
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
#目前<state>总是“failover",
# <role>是“Teader"或者"observer"中的-一个。
#参数from-ip， from-port， to-ip，to-port是用来和旧的master和新的master(即旧的s lave)通信的
#这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentine1 client-reconfig-script <master-name> <script-path>
sentine1 client-reconfig-script mymaster /var/redis/reconfig.sh #一般都是由运维来配置!
```

### 启动哨兵

```bash
# 哨兵会修改redis的配置文件
redis-sentinel /usr/local/etc/redis/sentinel.conf
```

## 缓存穿透和雪崩

### 缓存穿透

查不到导致

#### 概念

用户想要查询一个数据， 但是redis中没有， 就是说缓存没有命中， 于是上数据库查询， 发现也没有， 本次查询就会失败

当用户很多， 查询都没命中， 就会对数据库造成很大的压力

#### 解决方案

- 布隆过滤器

  布隆过滤器是一种数据结构， 对所有可能查询的参数以hash形式存储， 在控制层先进行校验， 不符合则丢弃

- 缓存空对象

  对数据库返回的空对象， 也缓存起来， 同时设置一个过期时间， 之后再访问这个数据将会从缓存中获取

  **缺点**， 浪费空间去存储空对象， 缓存层和持久层的数据会有一段时间难以保持一致， 这对于需要保持一致性的业务会有影响

### 缓存击穿

热点数据缓存突然失效导致

#### 概念

和缓存穿透的区别， 缓存击穿是指一个key非常的热点， 在不停的扛着大并发， 并且这个key存在失效时间， 当这个key突然失效， 持续的大并发就会穿破缓存， 直接请求数据库， 这样的数据叫做热点数据

#### 解决方案

- 设置热点数据， 永不过期
- 加互斥锁（分布式锁）

### 缓存雪崩

在某一个时段内， 缓存集体失效导致或者redis宕机

#### 概念

在双十一这样的活动， 12点秒杀时， 商品数据集体进入缓存， 假设有效期只有1小时， 到了1点， 这些数据集体失效， 大量的请求会到达数据库

### 解决方案

- 可以多增加redis， 搭建集群

- 限流降级

  在缓存失效后， 通过加锁或者队列来控制读取数据库写缓存的线程数量

- 数据预热

  把可能的数据先访问一边， 设置不同的过期时间

