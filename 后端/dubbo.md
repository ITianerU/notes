# 注册中心

## zookeeper

### 集群搭建

#### 步骤

- 安装jdk
- 创建data目录， 每个节点有自己的data
- 将conf目录下的zoo_sample.cfg改名为zoo.cfg
- 修改zoo.cfg文件中的dataDir， 指定为data目录， 和clientPort， 指定端口号， 同一台服务器部署多个zookeeper端口号不同

### 配置集群

#### 步骤

- 在data中创建文件myid，  内容为1，  第二个节点为2， 第三个节点为3， 内容为每个zookeeper的服务id
- 在zoo.cfg中配置

```properties
# zoopeeper集群为主从结构， 主节点是通过服务之间投票选举出的
# server.服务id=服务ip地址:服务端口：服务之间投票选举端口
server.1=ip:2881:3881
server.2=ip:2882:3882
server.3=ip:2883:3883
```

### 启动

```bash
sh /bin/zkServer.sh start
# 查看主从， 返回的是leader的是主节点
sh /bin/zkServer.sh status
```

# 服务接口

提供dubbo服务的接口， provider会实现服务接口， consumer通过服务接口调用具体服务

## 步骤

- 创建spring boot服务
- 创建接口， 与实体类
- 打包
- provider和consumer引入jar包

# Provider

## 依赖

```xml
<!-- springboot -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>log4j-to-slf4j</groupId>
            <artifactId>org.apache.logging.log4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- dubbo -->
<dependency>
    <groupId>com.alibaba.spring.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>
<!-- zookeeper -->
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.6.1</version>
</dependency>
<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.11</version>
    <exclusions>
        <exclusion>
            <groupId>slf4j-log4j12</groupId>
            <artifactId>org.slf4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- 服务api -->
<dependency>
    <groupId>com.itianeru</groupId>
    <artifactId>rocketmq-dubbo-interface</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

## 配置文件

```yml
spring:
  application:
    name: rocketmq-dubbo-provider
  dubbo:
    application:
      # id: rocketmq-dubbo-provider
      # 当前应用名称，用于注册中心计算应用间依赖关系
      name: rocketmq-dubbo-provider
    registry:
      # 注册中心服务器地址
      address: zookeeper://192.168.2.118:2181;zookeeper://192.168.2.118:2182;zookeeper://192.168.2.118:2183
    # 表示服务提供方
    server: true
    protocol:
      # 服务协议
      name: dubbo
      # 暴露的端口号
      port: 20880
```

## 启动类

启动类添加注解， 启用dubbo

```java
@EnableDubboConfiguration
```

## 服务类

```java
@Component
// 暴露服务
@Service(interfaceClass = IUserService.class)
public class UserServiceImpl implements IUserService {

    @Override
    public String sayHello(String name) {
        return "Hello：" + name;
    }
}
```

# 监控服务

在部署zookeeper的服务器上,  在tomcat中放入dubbo-admin的war包， 并启动tomcat

访问服务，  http:服务器ip:8080/dubbo-admin

访问账号/密码   root/root