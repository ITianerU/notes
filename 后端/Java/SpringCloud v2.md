# 概述

## 什么是微服务架构

​	微服务架构是一种架构模式, 它提倡将单一应用程序划分成一小组的服务, 服务之间互相协调, 互相配合, 为用户提供最终价值. 每个微服务运行在独立的进程中, 服务与服务之间采用轻量级的通信机制互相协作**(通常是基于http协议的RESTful API)**. 每个服务都围绕着具体的业务进行构建, 并且都能独立部署到生产环境. 对于一个具体的服务而言, 应根据业务需求, 选择合适的语言, 工具对齐进行构建

## 微服务架构应包含哪些部分

- 服务注册与发现
- 服务调用
- 服务熔断
- 负载均衡
- 服务降级
- 服务消息队列
- 配置中心管理
- 服务网关
- 服务监控
- 全链路追踪
- 自动化部署
- 服务定时任务调度操作

## SpringBoot和SpringCloud的区别

- SpringBoot是一种技术, 微服务开发框架
- SpringCloud是一整套微服务架构解决方案

## 什么是服务治理

在传统的rpc远程调用框架中, 管理每个服务与服务之间依赖关系比较复杂, 所以需要使用服务治理, 管理服务与服务之间的依赖关系, 可以实现服务调用, 复杂均衡, 容错等, 实现服务发现与注册.

## 什么是服务注册与发现

客户端链接到注册中心, 并维持心跳链接, 系统维护人员, 可用通过注册中心的监控系统监控各个服务是否正常工作,

在服务启动时, 服务会将自己的服务地址, 通讯地址等以别名的方式注册到注册中心上

服务调用方, 以别名的方式, 去注册中心获取要调用的服务的通讯地址

# 文档

- https://spring.io/projects/spring-cloud#learn  (SpringCloud文档)
- https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md  (中文文档. 有点过时)
- https://docs.spring.io/spring-boot/docs  (SpringBoot文档)

# 版本选择

### 查看对应版本
```properties
# https://spring.io/projects/spring-cloud#overview
打开这个链接在Table 1. Release train Spring Boot compatibility这能看到大概的版本对应表格
点击learn tab页 在Documentation表格, 点击对应版本的Reference Doc(文档). 可以看到, 这个SpringCloud版本推荐使用的SpringBoot版本
# https://start.spring.io/actuator/info
这个链接返回一个json, 可以看到稍微详细一些的版本对应信息
```

## 2020年

- SpringBoot   2.2.x | 2.3.x 版本
- SpringCloud    H版本

# **组件停更 | 替换 说明**

## 注册中心

- **Eureka**  停更不停用, 不推荐
- **Zookeeper**  可以使用
- **Consul**  部分版本国内禁止使用, 不推荐
- **Nacos**  阿里的组件, 推荐使用

## 服务调用

- **Ribbon**  停更不停用, 问题较小, 还可以继续使用 (2020年)
- **LoadBalancer**  逐渐替代Ribbon
- **Feign**   停止使用
- **OpenFeign**   代替了Feign

## 服务降级

- **Hystrix**  逐渐淘汰
- **resilience4j**   国外流行
- **sentienl**  阿里的组件, 推荐使用

## 服务网关

- **Zuul**  逐步淘汰
- **gateway**  推荐使用

## 服务配置

- **Config**  逐步淘汰
- **Nacos**  阿里的组件, 推荐使用

## 服务总线

- **Bus**  逐步淘汰
- **Nacos**  阿里的组件, 推荐使用

# 注册中心

## Eureka

### Eureka Server

提供服务注册服务

各个微服务节点通过配置启动后, 会在EurekaServer中进行注册, 这样EurekaServer中的服务注册表中将会存储所有可用的服务节点信息, 服务节点的信息在监控页面可以直观看到

### EurekaClient

客户端

用于简化与Eureka Server的交互, 客户端同时也具备一个内置的, 使用轮循(round-robin)负载算法的负载均衡器, 在应用启动后, 将会向Eureka Server发送心跳包(默认周期为30秒), 如果Eureka Server在多个心跳周期内, 没有接收到这个某个节点的心跳, Eureka Server会将从服务注册表中把这个服务节点移除(默认90秒)

# 项目搭建

## 创建父项目

- New Project -> Maven -> 勾选 Create from archetype(从原型创建) -> 选择 org.apache.maven.archetypes:maven-archetype-site
- 填写项目相关信息, 完成创建
- 如果pom文件<artifactId>maven-project-info-reports-plugin</artifactId>报错, 添加如下依赖, 下载完插件后, 删除依赖即可

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-project-info-reports-plugin</artifactId>
        <version>3.1.2</version>
        <type>maven-plugin</type>
    </dependency>
</dependencies>
```

- 删除src文件夹
- 修改pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itianeru</groupId>
    <artifactId>SpringCloudDemo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>cloud-provider-payment8001</module>
    </modules>
    <packaging>pom</packaging>

    <!-- 统一管理jar包 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>8.0.26</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>

    <!-- 子模块继承之后, 提供作用: 锁定版本 + 子module不用写groupId和version -->
    <dependencyManagement>
        <dependencies>
            <!-- spring boot 2.3.12.RELEASE -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.3.12.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- spring cloud Hoxton.SR12 -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR12</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- spring cloud alibaba 2.1.0 -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- mysql -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <!-- druid -->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <!-- mybatis -->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <!-- junit -->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <!-- log4j -->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <!-- lombok -->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <!-- 热部署插件 -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## IDEA配置

### 修改文件字符编码

- settings -> Editor -> File Encodings

  将如下配置都改为 UTF-8

  - Global Encoding
  - Project Encoding
  - Default encoding for properties files 

### 开启注解处理器

不开启可能会导致Lombok无法使用等问题

- settings -> Build, Execution, Deployment -> Compiler -> Annotation Processors
  - Default
    -  勾选 Enable annotation processing

### 修改Java编译器版本

- settings -> Build, Execution, Deployment -> Compiler -> Java Compiler
  - 修改 Target bytecode version

## 子项目

### 公共模块

用于存放, 各个模块的api和实体类

#### 父pom

```xml
<!-- 在父pom中加入子模块， 正常在创建子模块时会自动生成 -->
<modules>
    <module>cloud-provider-payment8001</module>
</modules>
```

#### pom文件

```xml
<parent>
    <artifactId>SpringCloudDemo</artifactId>
    <groupId>com.itianeru</groupId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>

<artifactId>cloud-api-commons</artifactId>

<properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
</properties>

<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <!-- 糊涂工具包 -->
    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
        <version>5.3.8</version>
    </dependency>
</dependencies>
```

#### POJO

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CommonResult <T>{
    private Integer code;
    private String message;
    private T data;
    public CommonResult(Integer code, String message){
        this(code, message, null);
    }
}
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Payment implements Serializable {
    private static final long serialVersionUID = -3545636279665905984L;
    private Long id;
    private String serial;
}
```

### 不使用SpringCloud的版本

#### 支付模块

给订单模块提供支付服务

##### 父pom

```xml
<!-- 在父pom中加入子模块， 正常在创建子模块时会自动生成 -->
<modules>
    <module>cloud-provider-payment8001</module>
</modules>
```

##### pom文件

```xml
<parent>
    <artifactId>SpringCloudDemo</artifactId>
    <groupId>com.itianeru</groupId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>

<artifactId>cloud-provider-payment8001</artifactId>

<properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
</properties>

<dependencies>
    <!-- 会从org.springframework.boot:spring-boot-dependencies找到对应版本的依赖依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-test</artifactId>
         <scope>test</scope>
    </dependency>
	
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
   
</dependencies>
```

##### 配置文件

```yml
server:
  port: 8001

spring:
  application:
    name:  cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/springcloud?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.itianeru.springcloud.pojo
```

##### SQL

```sql
CREATE TABLE `payment` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `serial` varchar(200) CHARACTER SET sjis DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

##### Controller

```java
@RequestMapping("/payment")
@RestController
@Slf4j
public class PaymentController {
    @Resource
    private PaymentService paymentService;
    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        if (payment != null){
            return new CommonResult(200, "成功哈", payment);
        }else{
            return new CommonResult(200, "没有该条数据");
        }
    }
    @PostMapping
    public CommonResult add(@RequestBody Payment payment) {
        int result = paymentService.add(payment);
        if(result == 1){
            return new CommonResult(200, "成功", result);
        }
        return new CommonResult(500, "失败");
    }
}
```

##### Service

```java
@Service
public class PaymentServiceImpl implements PaymentService {

    @Resource
    private PaymentDao paymentDao;

    @Override
    public Payment getPaymentById(Long id) {
        return paymentDao.getPaymentById(id);
    }

    @Override
    public int add(Payment payment) {
        return paymentDao.add(payment);
    }
}
```

##### Dao

```java
@Mapper
public interface PaymentDao {
    int add(Payment payment);
    Payment getPaymentById(@Param("id") Long id);
}
```

##### Mapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itianeru.springcloud.dao.PaymentDao">
    <resultMap id="baseResultMap" type="com.itianeru.springcloud.pojo.Payment">
        <id column="id" property="id" jdbcType="BIGINT"></id>
        <result column="serial" property="serial" jdbcType="VARCHAR"></result>
    </resultMap>
    <select id="getPaymentById" parameterType="Long" resultMap="baseResultMap">
        select * from payment where id = #{id}
    </select>
    <insert id="add" parameterType="com.itianeru.springcloud.pojo.Payment" useGeneratedKeys="true" keyProperty="id">
        insert into payment(serial) values(#{serial});
    </insert>
</mapper>
```

#### 订单模块

##### 父pom

```xml
<!-- 在父pom中加入子模块， 正常在创建子模块时会自动生成 -->
<modules>
    <module>cloud-consumer-order80</module>
</modules>
```

##### pom文件

```xml
<parent>
    <artifactId>SpringCloudDemo</artifactId>
    <groupId>com.itianeru</groupId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>

<artifactId>cloud-consumer-order80</artifactId>

<properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
</properties>

<dependencies>
    <!-- 该服务, 不涉及数据库, 所以没有数据库相关依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
</dependencies>
```

##### 配置文件

```yml
server:
  port: 80
spring:
  application:
    name: cloud-order-service
```

##### java配置

```java
@Configuration
public class ApplicationContextConfig {
    // 创建RestTemplate的Bean
    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

##### Controller

```java
@RestController
@RequestMapping("order")
public class OrderController {
    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        return restTemplate.getForObject("http://localhost:8001/payment/" + id, CommonResult.class);
    }

    @PostMapping
    public CommonResult add(Payment payment) {
        // 这里使用post调用支付服务的接口时, 传参需要支付服务接口有@RequestBody注解
        return restTemplate.postForObject("http://localhost:8001/payment", payment, CommonResult.class);
    }
}
```

### 使用Eureka的版本

在不使用注册中心的版本基础上, 进行修改

#### Eureka注册中心

##### 父pom

```xml
<!-- 在父pom中加入子模块， 正常在创建子模块时会自动生成 -->
<modules>
    <module>cloud-eureka-server7001</module>
</modules>
```

##### pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringCloudDemo</artifactId>
        <groupId>com.itianeru</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-eureka-server7001</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- 注册中心 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
    </dependencies>
</project>
```

##### 配置文件

```yml
server:
  port: 7001

spring:
  application:
    name: cloud-eureka-server

eureka:
  instance:
    hostname: localhost           # eureka服务端的实例名称
  client:
    register-with-eureka: false   # false表示不注册自己
    fetch-registry: false         # false表示自己就是注册中心, 我的职责是维护服务实例, 并不需要检索服务
    service-url:
      # 设置与Eureka Server交互地址, 查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/     
```

##### 启动类

```java
@SpringBootApplication
// 开启服务注册中心
@EnableEurekaServer   
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class, args);
    }
}
```

##### 测试

http://localhost:7001/

#### 支付模块

##### pom文件

```xml
<dependencies>
    <!-- 添加Eureka客户端依赖包 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

##### 配置文件

```yml
eureka:
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://localhost:7001/eureka   # 注册中心地址
```

##### Controller

```java
@RequestMapping("/payment")
@RestController
@Slf4j
public class PaymentController {
    @Resource
    private PaymentService paymentService;
    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        if (payment != null){
            return new CommonResult(200, "成功哈", payment);
        }else{
            return new CommonResult(200, "没有该条数据");
        }
    }
    @PostMapping
    public CommonResult add(@RequestBody Payment payment) {
        int result = paymentService.add(payment);
        if(result == 1){
            return new CommonResult(200, "成功", result);
        }
        return new CommonResult(500, "失败");
    }
}
```

##### 启动类

```java
@SpringBootApplication
// 添加启动eureka客户端注解
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
```



#### 订单模块

##### pom文件

```xml
<dependencies>
    <!-- 添加Eureka客户端依赖包 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

##### 配置文件

```yml
eureka:
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://localhost:7001/eureka   # 注册中心地址
```

##### java配置

```java
@Configuration
public class ApplicationContextConfig {
    // 创建RestTemplate的Bean
    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

##### Controller

```java
@RestController
@RequestMapping("order")
public class OrderController {
    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        return restTemplate.getForObject("http://localhost:8001/payment/" + id, CommonResult.class);
    }

    @PostMapping
    public CommonResult add(Payment payment) {
        // 这里使用post调用支付服务的接口时, 传参需要支付服务接口有@RequestBody注解
        return restTemplate.postForObject("http://localhost:8001/payment", payment, CommonResult.class);
    }
}
```

##### 启动类

```java
@SpringBootApplication
// 添加启动eureka客户端注解
@EnableEurekaClient
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class, args);
    }
}
```
