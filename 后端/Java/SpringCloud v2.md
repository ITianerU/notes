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

## Eureka工作流程

- 先启动Eureka注册中心
- 启动服务提供者, 服务启动后, 会把自己的信息(服务地址等)注册到注册中心
- 消费者需要调用服务提供者的接口时, 使用服务提供者的别名去注册中心获取真实地址, 然后使用真实地址去调用接口(低层是用httpClient)
- 消费者获得服务地址后, 会缓存在本地jvm内存中, 默认每间隔30秒更新一次服务调用地址

## 微服务远程调用最核心的是什么

高可用, 搭建集群, 实现负载均衡+故障容错

## Eureka自我保护机制

当页面出现如下红色文字时, 说明Eureka进入了自我保护机制

<span style="color:red">EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.</span>

默认情况下, 注册中心一定时间没有收到某个微服务的心跳, 90秒没收到就会注销该微服务, 但是可能会发生, 微服务正常, 但是微服务与注册中心的网络发生故障, 此时, 不想让这个微服务被注销, 就出现了自我保护机制 

Eureka Server将会尝试保护其服务注册到注册表中的信息, 不再删除服务注册表中的数据,也就是说不会注销微服务,

就是说, 一个服务宕机了, 注册中心不会删除这个微服务, **属于CAP里面的AP,**

**触发条件:** 当注册中心短时间内, 出现大量微服务丢失的情况才会触发

## CAP原则

- C(Consistency)  强一致性
- A(Availability)  可用性
- P(Partition Tolerance)  分区容错性

一个分布式系统不可能同时很好的满足CAP, 因此根据CAP原则将NoSQL数据库分成了三种

- CA  单点集群, 满足一致性, 可用性的系统, 通常在扩展性不太强
- CP  满足一致性, 分区容错性的系统, 通常性能不高
- AP  满足可用性, 分区容错性的系统, 通常对一致性要求低一些

## 负载均衡是什么意思

简单的说就是将用户的请求,平均分配到多个服务上, 从而达到系统的HA(高可用)

常用的负载均衡软件Nginx, LVS, 硬件F5等

## Ribbon本地负载均衡和Nginx负载均衡的区别

Nginx是服务器负载均衡, 客户端所有请求都会交给Nginx, 然后由Nginx实现转发请求.是属于集中式的负载均衡

Ribbon本地负载均衡, 在调用微服务接口时候, 会在注册中心上获取注册信息服务列表之后缓存到JVM本地, 从而在本地实现RPC远程服务调用技术,属于进程内的负载均衡

## 什么是总线

在微服务架构的系统中, 通常会使用<span style="color:red">轻量级的消息代理</span>来构建一个<span style="color:red">共用的消息主题</span>, 并让系统中的所有微服务实例都连接上来, 由于<span style="color:red">该主题中产生的消息会被所有实例监听和消费, 所以称它为消息总线</span>

# 文档

- https://spring.io/projects/spring-cloud#learn  (SpringCloud文档)
- https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md  (中文文档. 有点过时)
- https://docs.spring.io/spring-boot/docs  (SpringBoot文档)
- https://www.consul.io/docs/intro (consul文档)
- https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md (spring cloud alibaba文档)
- https://github.com/alibaba/Sentinel/releases  (sentinel下载页面)
- https://sentinelguard.io/zh-cn/index.html (sentinel官网)
- http://seata.io/zh-cn/ (seata官网)

# 版本选择

## 查看对应版本
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

### 服务端配置

```yml
eureka:
  instance:
    hostname: eureka7001.com         # eureka服务端的实例名称
  client:
    register-with-eureka: false   # false表示不注册自己
    fetch-registry: false         # false表示自己就是注册中心, 我的职责是维护服务实例, 并不需要检索服务
    service-url:
      # 设置与Eureka Server交互地址, 查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```

### 客户端配置

```yml
eureka:
  instance:
    instance-id: cloud-payment-service8001  # 这里配置微服务名称, 用于在Eureka的监控页面显示
    prefer-ip-address: true                 # 鼠标放在服务名上, 左下角显示服务ip
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/

# info中配置的信息, 会被/actuator/info这个接口返回
# 在Eureka的监控页面, 点击服务名打开的页面会返回info里面自定义的数据
info:
  # 略
```

### 关闭自我保护模式

服务端添加如下配置

```yml
eureka:
  server:
    # 关闭自我保护机制
    enable-self-preservation: false
    # 表示 Eureka Server 清理无效节点的频率
    eviction-interval-timer-in-ms: 2000
```

客户端添加如下配置

```yml
eureka:
  instance:
    # 表示 Eureka Server 在接收到上一个心跳之后等待下一个心跳的秒数（默认 90 秒），若不能在指定时间内收到心跳，则移除此实例，并禁止此实例的流量。
    # 此值设置太长，即使实例不存在，流量也能路由到该实例 
    # 此值设置太小，由于网络故障，实例会被取消流量
    # 需要设置为至少高于 lease-renewal-interval-in-seconds 的值，不然会被误移除了。
    lease-expiration-duration-in-seconds: 30
    # 表示 Eureka Client 向 Eureka Server 发送心跳的频率（默认 30 秒），
    # 如果在 lease-expiration-duration-in-seconds 指定的时间内未收到心跳，则移除该实例。
    lease-renewal-interval-in-seconds: 15
```

## Zookeeper

zookeeper中注册进来的节点是临时的, 当服务停止, 在一段时间内, 该节点在zookeeper内会被注销

服务再次启动, 会重新注册, 比较前后两次的实例id可以看到, 两次的实例id不同

相对于Eureka, 在服务停止时会更快的注销节点, 没有自我保护机制

## Consul

### 简介

Consul是一套开源的分布式服务发现和配置的管理系统, 使用go语言开发

提供了微服务系统中的服务治理, 配置中心, 控制总线等功能. 这些功能中的每一个都可以根据需要单独使用, 也可以一起使用以构建全方位的服务网络

总之Consul提供了一种完整的服务网络解决方案

## Nacos

## 四个注册中心异同点

|  组件名   | 语言 |   CAP    | 服务健康检查 | 对外暴露监控页面 | SpringCloud集成 |
| :-------: | :--: | :------: | :----------: | :--------------: | :-------------: |
|  Eureka   | Java |    AP    |  可配置支持  |      有页面      |     已集成      |
|  Consul   |  Go  |    CP    |     支持     |      有页面      |     已集成      |
| Zookeeper | Java |    CP    |     支持     |      无页面      |     已集成      |
|   Nacos   | Java | AP \| CP |     支持     |      有页面      |     已集成      |

# 服务调用

## Ribbon

### 简介

Spring Cloud Ribbon是基于NetFlix Ribbon实现的一套负载均衡的工具

主要功能是提供客户端软件的**负载均衡算法和服务调用**, Ribbon客户端组件提供一系列完善的配置项, 如连接超时, 重试等. 

### 依赖

```xml
<!-- spring-cloud-starter-netflix-eureka-client客户端依赖已经集成了Ribbon, 无需手动引入 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
```

### 配置类

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced  // 开启负载均衡
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

### 用法

```java
@RestController
@RequestMapping("order")
public class OrderController {
    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        // 返回json
        return restTemplate.getForObject("http://cloud-payment-service/payment/" + id, CommonResult.class);
    }

    @PostMapping
    public CommonResult add(Payment payment) {
        // 返回json
        return restTemplate.postForObject("http://cloud-payment-service/payment", payment, CommonResult.class);
    }
    
    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        // 返回数据携带请求信息
        ResponseEntity<CommonResult> forEntity = restTemplate.getForEntity("http://cloud-payment-service/payment/" + id, CommonResult.class);
        if (forEntity.getStatusCode().is2xxSuccessful()){
            return forEntity.getBody();
        }else{
            return new CommonResult<>(500, "失败");
        }
    }
    
    @PostMapping
    public CommonResult add(Payment payment) {
        // 返回数据携带请求信息
        ResponseEntity<CommonResult> forEntity = restTemplate.postForEntity("http://cloud-payment-service/payment", payment, CommonResult.class);
        if (forEntity.getStatusCode().is2xxSuccessful()){
            return forEntity.getBody();
        }else{
            return new CommonResult<>(500, "失败");
        }
    }
}
```

### 负载均衡算法

| 策略类                    | 命名             | 说明                                                         | 原理                                                         |
| ------------------------- | ---------------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| RandomRule                | 随机策略         | 随机选择 Server                                              |                                                              |
| RoundRobinRule            | 轮训策略         | 按顺序循环选择 Server                                        | rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标, <br />每次服务启动后rest接口计数从1开始 |
| RetryRule                 | 重试策略         | 先按照轮询的方式获取服务, 在一个配置时问段内当选择 Server 不成功，<br />则一直尝试选择一个可用的 Server |                                                              |
| BestAvailableRule         | 最低并发策略     | 逐个考察 Server，如果 Server 断路器打开，则忽略，再选择其中并发连接最低的 Server |                                                              |
| AvailabilityFilteringRule | 可用过滤策略     | 过滤掉一直连接失败并被标记为 `circuit tripped` 的 Server，过滤掉那些高并发连接的 Server（active connections 超过配置的网值） |                                                              |
| ResponseTimeWeightedRule  | 响应时间加权策略 | 优化了随机策略 根据 Server 的响应时间分配权重。<br />响应时间越长，权重越低，被选择到的概率就越低；响应时间越短，权重越高，被选择到的概率就越高。<br />这个策略很贴切，综合了各种因素，如：网络、磁盘、IO等，这些因素直接影响着响应时间 |                                                              |
| ZoneAvoidanceRule         | 区域权衡策略     | 综合判断 Server 所在区域的性能和 Server 的可用性轮询选择 Server，并且判定一个 AWS Zone 的运行性能是否可用，剔除不可用的 Zone 中的所有 Server |                                                              |

### 自定义负载均衡规则

**注:**  类不能再启动类同级的文件夹下, 就是不能被启动类扫描到, 如下

- com.itianeru
  - myrule    // 这个文件夹下, 自定义规则
    - MySelfRule   // 自定义规则配置类
    - CustomRule   // 自定义规则类
  - springcloud   // 这个文件夹下为业务代码
    - controller
    - service
    - dao
    - main.java   // 启动类

#### 自定义规则类

```java
public class CustomRule extends AbstractLoadBalancerRule {
    private AtomicInteger atomicInteger = new AtomicInteger(0);
    public CustomRule() {
    }

    @Override
    public void initWithNiwsConfig(IClientConfig iClientConfig) {

    }

    @Override
    public Server choose(Object key) {
        return this.choose(this.getLoadBalancer(), key);
    }

    public Server choose(ILoadBalancer lb, Object key) {
        System.out.println("使用了自定义的轮循算法");
        List<Server> upList = lb.getReachableServers();
        int index = getAndIncrement() % upList.size();
        return upList.get(index);
    }

    public final int getAndIncrement(){
        int current;
        int next;
        do{
            current = this.atomicInteger.get();
            next = current >= Integer.MAX_VALUE ? 0 : current + 1;
        }while (!this.atomicInteger.compareAndSet(current, next));
        return next;
    }
}
```

#### 自定义规则配置类

```java
@Configuration
public class MySelfRule {
    
    @Bean
    public IRule myRule(){
        // 可以使用Riboon提供的负载均衡算法
        // return new RandomRule();
        // 也可以使用自定义的负载均衡算法
        return new CustomRule();
    }
}
```

#### 启动类配置自定义类

```java
@SpringBootApplication
@EnableEurekaClient
// 只配置一个服务, 注: name的值为controller调微服务时, 如果是大写, 这里就要大写, 如果是小写, 这里就要小写
// @RibbonClient(name = "cloud-payment-service", configuration = MySelfRule.class)
// 配置多个服务
@RibbonClients(
    @RibbonClient(name = "cloud-payment-service", configuration = MySelfRule.class)
)
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class, args);
    }
}
```

### 超时控制

ribbon默认只等待1秒钟

#### 配置

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        SimpleClientHttpRequestFactory simpleClientHttpRequestFactory = new SimpleClientHttpRequestFactory();
        simpleClientHttpRequestFactory.setConnectTimeout(10000);
        simpleClientHttpRequestFactory.setReadTimeout(10000);
        return new RestTemplate(simpleClientHttpRequestFactory);
    }
}
```

## OpenFeign

### 简介

是一个声明式WebService客户端, 使用Feign能让编写WebService客户端更加简单

它的使用方法是定义一个服务接口然后在上面添加注解,

内置Ribbon

### 对比Feign

|                            Feign                             |                          OpenFeign                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| Feign是SpringCloud组件中一个轻量级RESTFul的http客户端, Feign内置了Ribbon, 用来做客户端负载均衡, 去调用服务注册中心的服务.<br />Feign的使用方式是: 使用Feign的注解定义接口, 调用这个接口, 就可以调用服务注册中心的服务 | OpenFeign是在Feign的基础上支持了SpringMVC的注解, 如@RequestMapping等等, <br />OpenFeign的@FeignCLient可以解析SpringMVC的@RequestMapping注解下的接口, 并通过动态代理的方式产生实现类, 实现类中做负载均衡并调用其他服务 |

### 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

### service

```java
@Service
// @FeignClient指定要调用的服务名, 如果同一个服务有多个controller, 需要通过contextId来区分,
// path为controller的路径
@FeignClient("CLOUD-PAYMENT-SERVICE", contextId = "PaymentOpenFeignService1", path = "/payment")
public interface PaymentOpenFeignService {
	
    // 与服务的controller路径相同
    @GetMapping("/{id}")
    CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);

    @PostMapping
    CommonResult add(@RequestBody Payment payment);
}
```

### 超时控制

OpenFeign的超时控制, 底层还是使用的Ribbon, 所以修改Ribbon的超时设置即可

```yml
# 设置ribbon客户端超时时间
ribbon:
  # 调接口时间
  ReadTimeout: 5000
  # 建立连接时间
  ConnectTimeout: 5000
```

### 日志增强

OpenFeign提供了日志打印功能, 可以通过调整日志级别, 从而了解OpenFeign中Http请求细节

- **NONE**   默认, 不显示任何日志
- **BASIC**   仅记录请求方法, URL, 响应状态码及执行时间
- **HEADERS**   除了BASIC的信息, 还有请求和响应头信息
- **FULL**  除了HEADERS中定义的信息之外, 还有请求和响应的正文及元数据

### 配置

#### Java配置日志级别

```java
@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
```

#### yml开启日志

```yml
# 指定要开启日志的服务
logging:
  level:
  	# 可以指定多个
    com.itianeru.springcloud.service.PaymentOpenFeignService: debug
    com.itianeru.springcloud.service.PaymentOpenFeignService2: debug
```

### 自定义负载均衡规则

因为OpenFeign底层使用的还是Ribbon, 所以负载均衡的配置和Ribbon一样

# 服务降级

## Hytrix

已经停止更新.

用于处理分布式系统的延迟和容错的开源库, 在分布式系统里, 许多依赖不可避免的会调用失败, 比如超时, 异常等, Hystrix能保证在一个依赖出问题的情况下, 不会导致整体服务失败, 避免级联故障, 以提高分布式系统的弹性.

断路器本身是一种开关装置, 当某个服务单元发生故障之后, 通过断路器的故障监控, 向调用方返回一个符合预期的, 可处理的备选响应, 而不是长时间等待或者抛出无法处理的异常, 这样就保证了服务调用方的线程不会被长时间, 不必要的占用, 从而避免了故障在分布式系统中的蔓延, 乃至雪崩

### 服务降级(fallback)

常用于消费端

- 服务器发生故障时, 立刻返回一个备选方案
- 哪些情况会触发降级
  - 程序运行异常
  - 超时
  - 服务熔断触发服务降级
  - 线程池/信号量打满也会导致服务降级

### 服务熔断

常用于服务端

- 类比保险丝, 当服务达到最大访问量, 直接拒绝访问, 然后调用服务降级方法, 防止服务器崩溃, 等检测到服务正常会自己恢复

### 服务限流

- 秒杀高并发等操作, 将请求排队, 有序进行, 限制流量

### 服务监控

#### 仪表盘

##### 依赖

```xml
<dependency>
    <!-- 增加Hystris-dashboard包 -->
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

##### 配置文件

```yml
server:
  port: 9001
hystrix:
  dashboard:
    # 指定允许监听的ip
    proxy-stream-allow-list: localhost
```

##### 启动类

```java
@SpringBootApplication
@EnableHystrixDashboard
public class HystrixDashbordMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixDashbordMain9001.class, args);
    }
}
```

##### 访问地址

- http://localhost:9001/hystrix
- 输入要监控的服务ip  http://要监控的服务ip/hystrix.stream

#### 被监控的服务

##### 配置类

```java
@Bean
public ServletRegistrationBean getServlet(){
    HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
    ServletRegistrationBean<HystrixMetricsStreamServlet> registrationBean = new ServletRegistrationBean(streamServlet);
    registrationBean.setLoadOnStartup(1);
    registrationBean.addUrlMappings("/hystrix.stream");
    registrationBean.setName("HystrixMetricsStreamServlet");
    return registrationBean;
}
```

## Sentinel

阿里巴巴开源的流量监控组件

### 流控规则

#### 创建

##### 导航

```
簇点链路->流控
```

##### 字段解释

- **资源名**  RequestMapping映射的地址 或者@SentinelResource的value
- **针对来源**   默认即可
- **阈值类型**   
  - **QPS**   每秒请求数
  - **线程数**   使用的线程数
- **单机阈值**
  - **QPS**   调用该API的QPS达到阈值时候, 进行限流  
  - **线程数**   调用该API的线程数达到阈值的时候, 进行限流
- **流控模式**
  - **直接**    超过阈值, 直接触发流控效果
  - **关联**    当关联的资源达到阈值的时候, 限流自己
    - **关联资源**   关联的RequestMapping映射的地址
  - **链路**
- **流控效果**
  - **快速失败**   直接返回一条失败信息
  - **Warm Up(预热)**   逐步提高阈值到目标阈值, 默认的初始阈值是目标阈值 / 3(冷加载因子默认是3)
    - **预热时长**   多少秒达到目标阈值
  - **排队等待**   请求排队, 匀速处理, 阈值为每秒可以处理的请求
    - **超时时间**   请求超时时间

#### 自定义降级方法

```java
@RequestMapping("/testE")
// value是id,配在控制台上, blockHandler指定降级的方法
// 如果没有指定blockHandler降级方法, 那么在创建规则的时候, 如果选择的是@SentinelResource的value, 那么不会使用系统默认的返回值, 而是直接打印错误信息
@SentinelResource(value = "testE", blockHandler = "deal_testE")
public String testD(@RequestParam("p1") String param1, @RequestParam(value = "p2", required = false)String param2) {
    return "-----------testE";
}

public String deal_testE(String param1, String param2, BlockException exception){
    return "------------del testE";
}
```

#### 编辑 | 删除

##### 导航

```
流控规则->编辑 | 删除
```

### 降级规则

#### 创建

##### 导航

```
簇点链路->降级
```

##### 字段解释

- **资源名**  RequestMapping映射的地址
- **熔断策略**   
  - **慢调用比例**   API调用为慢调用的比例达到比例阈值, 对服务降级
    - **最大RT**   多长时间调用为慢调用, 最大值为4900ms, 超过该值为慢调用
    - **比例阈值**   慢调用的比率 [0, 1], 超过这个触发熔断 **(1.8版本有bug, 设置无效)**
    - **熔断时长**   在这段时间内发生熔断、拒绝所有请求
    - **最小请求数**   允许通过的最小请求数，在该数量内不发生熔断
  - **异常比例**  API调用为异常的比率达到阈值, 对服务降级
    - **比例阈值**   异常比例=发生异常的请求数÷请求总数取值范围：[0~1]
    - **熔断时长 **  在这段时间内发生熔断、拒绝所有请求
    - **最小请求数 **  允许通过的最小请求数，在该数量内不发生熔断
  - **异常数**   请求发生异常的数量达到阈值, 触发降级
    - **异常数 **  请求发生异常的数量达到阈值
    - **熔断时长 **   在这段时间内发生熔断、拒绝所有请求 **(需要大于60秒, 因为他是检测1分钟内的异常数)**
    - **最小请求数**    许通过的最小请求数，在该数量内不发生熔断

#### 编辑 | 删除

##### 导航

```
降级规则->编辑 | 删除
```

### 热点规则

某些API接口的参数的值可能是热点值, 会被大批量的访问, 这时可以监控这个参数, 在一定时间内, 相同的参数值会被降级

使用时@SentinelResource注解中, 必须指定自定义的降级方法, 不然会将错误信息, 打印到页面上

#### 创建

##### 导航

```
簇点链路->热点
```

##### 字段解释

- **资源名**  @SentinelResource注解中, value对应的值
- **参数索引**   监控第几个参数的值
- **单机阈值**   这个参数的值被调用了几次
- **统计窗口时长**   在多长时间内, 达到单机阈值, 会触发降级

#### 编辑 | 删除

##### 导航

```
热点规则->编辑 | 删除
```

#### 代码

```java
@RequestMapping("/testE")
// value是id,配在控制台上, blockHandler指定降级的方法
@SentinelResource(value = "testE", blockHandler = "deal_testE")
public String testD(@RequestParam("p1") String param1, @RequestParam(value = "p2", required = false)String param2) {
    return "-----------testE";
}

public String deal_testE(String param1, String param2, BlockException exception){
    return "------------del testE";
}
```

#### 参数例外项

单独指定被监控的参数的某些热点值的阈值, 可配置多个值

##### 导航

```
热点规则->编辑->高级选项
```

##### 字段解释

- **参数类型**   支持六种基本数据类型加字符串
- **参数值**   参数的值
- **限流阈值**    这个参数值,达到多少会被降级

### 系统规则

对整个系统的全部API做降级配置**(慎用)**

### 独立的降级方法

#### controller

```java
@RestController
public class RateLimitController {

    @GetMapping("byResource")
    // 指定降级处理的类, 指定类中降级处理的方法
    @SentinelResource(value = "byResource", blockHandlerClass = CustomerBlockHandler.class, blockHandler = "handleException")
    public CommonResult byResource(){
        return new CommonResult(200, "按资源名称限流测试OK", new Payment(2020L, "serial001"));
    }
}
```

#### handler

```java
// 新建一个文件夹, myHandler 用于存放服务降级处理类
public class CustomerBlockHandler {
    // 类必须要用public static修饰
    public static CommonResult handleException(BlockException exception){
        return new CommonResult(500, exception.getClass().getCanonicalName() + "\t服务不可用");
    }
}
```

### fallback

fallback用于处理服务异常, 返回一个备选方案,

- **fallback**: 用于处理服务异常, 每次异常都会去处理
- **blockHandler**: 用于针对于sentinel的配置, 在配置规则内去调用指定的方法

fallback和blockHandler同时使用时, fallback会处理每次的异常, 当异常次数达到Sentinel配置的规则时, 会使用blockHandler指定的处理方法

```java
@GetMapping("test/{id}")
// fallback指定fallback方法名
@SentinelResource(value = "fallback", fallback = "fb")
public String getPayment2(@PathVariable("id")Integer id){
    int a = 10 / 0;
    return "";
}

public String fb(@PathVariable("id")Integer id, Throwable e){
    return "异常:" + e.getMessage();
}
// 如果想独立出一个文件, 和独立降价的使用方法相同 
@SentinelResource(value = "fallback", fallbackClass = xxx.class, fallback = "fb")
```

#### 排除异常

指定不需要处理的异常

```java
// exceptionsToIgnore = {NullPointerException.class} 排除空指针异常
@SentinelResource(value = "fallback", fallback = "fb", exceptionsToIgnore = {NullPointerException.class})
```

### 配置持久化

将sentinel的配置, 配置到nacos中, 防止服务重启后, sentinel配置丢失

#### 依赖

```xml
 <!-- sentinel持久化 -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

#### 配置文件

```yml
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8851   # nacos地址
    sentinel:
      transport:
        dashboard: localhost:8858 # sentinel监控地址
        port: 8719   # 默认端口, 假如被占用会自动从8719开始依次+1扫描, 直到找到未被占用的端口
      # 这里配置持久化
      datasource:
        ds1:
          nacos:
            server-addr: localhost:8851
            dataId: cloudalibaba-sentinel-service
            groupId: DEFAULT_GROUP
            data-type: json
            rule-type: flow

# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: '*'

```

#### 注册中心监控页面操作

```json
// 新建配置->Data ID为配置的dataId->配置格式为json->
// 配置内容, 和sentinel控制台的配置类似
[{
    "resource": "test01" // "资源名称",
    "limitApp": "default" // "来源应用",
    "grade": 1 //"阈值类型0表示线程数, 1表示QPS",
    "count": 1 // "单机阈值",
    "strategy":  0// "流控模式, 0表示直接, 1表示关联, 2表示链路",
    "controlBehavior": 0,  // 流控效果, 0表示快速失败, 1表示warm up, 2表示排队等待",
    "clusterMode": false  // 是否集群
}]
```

# 服务网关

## zuul

已弃用

## gateway

SpringCloud Gateway是基于WebFlux框架实现的, 而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty,

Gateway是基于**异步非阻塞模型**进行开发的, 有很高的性能

### Route(路由)配置

#### yml配置

```yml
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh                      # 路由的ID， 没有固定的规则， 但是要求唯一， 建议配合服务名
          uri: http://localhost:8001             # 匹配后的提供服务的路由地址
          predicates:
            - Path=/payment/get/**               # 断言， 路径相匹配的进行路由
        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/timeout/**
```

#### java配置(不推荐)

```java
@Configuration
public class GateWayConfig {
    @Bean
    public RouteLocator getRoutes(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        //               路由id唯一                      断言predicates                  路由地址
        routes.route("payment_routh", r -> r.path("/payment/get/**").uri("http://localhost:8001")).build();
        routes.route("payment_routh2", r -> r.path("/payment/timeout/**").uri("http://localhost:8001")).build();
        return routes.build();
    }
}
```

#### 动态路由配置

```yml
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true                          # 开启从注册中心动态创建路由功能, 利用微服务名进行路由
      routes:
        - id: payment_routh                      # 路由的ID， 没有固定的规则， 但是要求唯一， 建议配合服务名
          uri: lb://CLOUD-PAYMENT-SERVICE        # url指定服务集群的服务名, lb表示使用负载均衡
          predicates:
            - Path=/payment/get/**               # 断言， 路径相匹配的进行路由
        - id: payment_routh2
          uri: lb://CLOUD-PAYMENT-SERVICE
          predicates:
            - Path=/payment/timeout/**
```

### Predicate(断言)配置

#### After | Before | Between

```yml
predicates:
	- Path=/payment/timeout/**
	- After=2021-09-14T16:42:12.958+08:00[Asia/Shanghai]   # 在该时间之后服务生效
	- Before=2021-09-14T16:42:12.958+08:00[Asia/Shanghai]   # 在该时间之前服务生效
	- Between=2021-09-14T16:42:12.958+08:00[Asia/Shanghai],2022-09-14T16:42:12.958+08:00[Asia/Shanghai]   # 在两个时间之间服务生效
```

##### 可通过如下方法获取时间格式

```java
ZonedDateTime zbj = ZonedDateTime.now();
System.out.println(zbj);
```

#### Cookie

```yml
predicates:
	- Path=/payment/timeout/**
	- Cookie=token,token\d+ # 请求中必须存在一个 cookie 的key 是 token 的，且值必须是 token 后面加上一个数子才匹配的上，否则匹配不上
```

##### 测试

```bash
# cmd 运行
curl http://localhost:9527/payment/get/1 --cookie "token=token123456"
```

#### Header

```yml
predicates:
	- Path=/payment/timeout/**
	- Header=propName,propValue # 请求头中必须存在一个propName=propValue
```

##### 测试

```bash
# cmd 运行
curl http://localhost:9527/payment/get/1 -H "propName:propValue"
```

#### Host

```yml
predicates:
	- Path=/payment/timeout/**
	- Host=**.x.com   # 请求头Host的值必须要匹配**.x.com
```

##### 测试

```bash
# cmd 运行
curl http://localhost:9527/payment/get/1 -H "host:sb.x.com"
```

#### Method

```yml
predicates:
	- Path=/payment/timeout/**
	- Method=GET   # 指定get请求
```

##### 测试

```bash
# cmd 运行
curl http://localhost:9527/payment/get/1 -X GET 
```

#### Query

```yml
predicates:
	- Path=/payment/timeout/**
	- Query=name   # 指定请求必须有name参数
```

##### 测试

```bash
# cmd 运行
curl http://localhost:9527/payment/get/1?name=laowang
```

#### Weight

```yml
- id: payment_routh_High                   
    uri: http://localhost:8001            # 配置同一服务的不同服务地址
    predicates:
    - Path=/payment/get/**                 
    - Weight=group1,8                     # 配置权重 80%
- id: payment_routh_low                     
    uri: http://localhost:8002            
    predicates:
    - Path=/payment/get/**             
    - Weight=group1,2                     # 配置权重 20%
```

### Filter(过滤器)配置

#### 生命周期

- pre
- post

#### 种类

- GatewayFilter  单一
- GlobalFilter 全局

#### 自定义过滤器

```java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        Object username = exchange.getRequest().getQueryParams().get("username");
        if (StringUtils.isEmpty(username)){
            log.error("非法用户");
            // 设置拒绝访问状态码
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        log.info(username.toString());
        // 通过
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        // 数字越小, 优先级越高, 指的是多个过滤器的执行顺序
        return 0;
    }
}
```

# 服务配置

随着项目中模块越来越多, 配置文件也越来越多, 需要一个统一配置中心, 集中, 动态配置,

- 服务端

  服务端通过从远程仓库, 获取配置文件, 对外暴露配置文件

- 客户端

  客户端不从远程仓库获取配置文件, 从服务端获取配置文件,

  客户端使用**bootstrap.yml**配置文件, 相对与**application.yml**, **bootstrap.yml**是系统级的优先级更高,

## Config

### 读取方式

- /{ lable }/{ application }-{ profile }.yml

```bash
# master 分支
# 开发环境
http://localhost:3344/master/config-dev.yml
# 测试环境
http://localhost:3344/master/config-test.yml
# dev分支
http://localhost:3344/dev/config-dev.yml
```

### 手动触发动态更新

客户端从配置中心读取配置文件, 配置文件远程修改后, 无法自动刷新, 需要手动触发, 调用触发的接口

```
http://hostname:port/actuator/refresh
```

### 消息总线 

每个微服务订阅同一条消息, 当推送消息后, 触发配置更新, 需要借助消息中间件, 目前仅支持RabbitMQ 和kafka.

利用消息总线触发一个服务端的/bus/refresh端点, 而刷新所有客户端的配置

#### RabbitMQ

##### 安装

请查看docker笔记

## Nacos

Nacos也可以作为配置中心, 同springcloud-config一样, 在项目初始化时, 要保证先从配置中心进行配置拉取, 拉去配置后, 才能保证项目的正常启动,

自带动态刷新功能, 不需要消息总线, 手动刷新

### DataId配置规则

在nacos管理界面上配置

```yml
# Data ID配置规则  服务名-环境-后缀名, 注: 后缀名不能为 yml        
# ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
```

### 分类配置

分为三种配置, Namespace->Group->DataId

- Namespace(命名空间): 用来区分环境 开发, 测试, 生产
- Group(分组): 不同的微服务,划分到同一个分组里

**bootstrap.yml配置**

```yml
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848   # Nacos作为注册中心的地址
      config:
        server-addr: localhost:8848   # Nacos作为配置中心的地址
        file-extension: yaml          # 指定yaml格式的配置
        group: TEST_GROUP             # 读取自定义的测试环境分组
```

**application.yml配置**

```yml
spring:
  profiles:
    active: info
```

- DataId: 微服务的配置文件名

**bootstrap.yml配置**

```yml
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848   # Nacos作为注册中心的地址
      config:
        server-addr: localhost:8848   # Nacos作为配置中心的地址
        file-extension: yaml          # 指定yaml格式的配置
        # group 默认为DEFAULT_GROUP
```

**application.yml配置**

```yml
spring:
  profiles:
#    active: dev  # 表示要从配置中心拉取什么环境的配置文件
    active: test  # 表示要从配置中心拉取什么环境的配置文件
```

# 消息驱动

当一个项目使用了多种MQ, 开发者不一定会每个MQ, SpringCloud Stream屏蔽了底层消息中间件的差异, 降低切换成本, 统一消息的编程模型

**目前仅支持RabbitMQ和Kafka**

## 重复消费

默认是广播模式,  同一个group下的全部服务, 只有一个能消费, 不同group都会消费到 

默认分组为随机分配的流水号, 每个服务都不同, 

通一分组的默认消费策略是轮循

## 持久化消费

如果一个服务没有指定分组, 当服务宕机, 再重启, 这期间的MQ消息, 该服务不会继续消费

指定group的服务, 会消费掉宕机期间的MQ

# 链路追踪

## Sleuth

在微服务框架中, 一个由客户端发起请求的后端系统会经过多个不同的服务节点调用来协同产生最后的请求结果, 每一个前端请求都会形成一条复杂的分布式服务调用链路, 链路中的任何一环出现高延时或错误都会引起整个请求最后的失败,

通过整合Zipkin实现

### Zipkin

一条链路通过Trace Id唯一标识, Span标识发起请求的信息, 各Span通过parent id关联起来

```text
Span id = A      ->    Span id = B      -> Span id = C
Parent id = null       Parent id = A       Parent id = B
```

#### 安装

```bash
# 官网 支持docker
https://zipkin.io/pages/quickstart.html
```

# 分布式事务

## Seata

Seata是一款开源的分布式事务解决方案, 致力于在微服务架构下提供高性能和简单的易用的事务服务.

有一个全局事务ID和三个组件TC, TM和RM组成 

**注:  1.0之前的版本不支持集群**

- **TC(事务协调者)**  维护全局和分支事务的状态，驱动全局事务提交或回滚。**(一般是指Seata服务器)**
- **TM(事务管理器)**  定义全局事务的范围：开始全局事务、提交或回滚全局事务。**(事务发起方)**
- **RM(资源管理器)**  管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。**(事务的参与方)**

### 步骤

- TM向TC申请开启一个全局事务, 全局事务创建成功, 并返回一个全局唯一的XID
- XID在微服务的调用链路的上下文中传播
- RM向TC注册分支事务, 将其纳入XID对应的全局事务的管辖
- TM向TC发起针对XID的全局提交或回滚决议
- TC调度XID下管辖的全部分支事务完成提交或回滚 

### 提交 | 回滚的步骤

#### 第一阶段

- Seata拦截业务SQL,  解析SQL语义, 找到业务SQL要更新的业务数据, 在业务数据要更新前, 将其保存成**前置镜像**
- 执行业务SQL, 在业务数据更新后, 生成**后置镜像**,
- 最后对该数据生成行锁
- 以上操作都在一个事务内完成, 保证了操作的原子性

#### 提交阶段

- 如果业务代码没有发生异常, 顺利提交
- 因为业务SQL在一阶段已经提交到数据库, 所以Seata只需要将保存的镜像删除, 解除行锁即可

#### 回滚阶段

- 如果发生了异常
- Seata就需要回滚一阶段已经执行的业务SQL, 还原业务数据
- 首先, 需要对比数据库当前业务数据, 和后置镜像, 如果两份数据一致, 说明没有脏写, 如果不一致, 需要转人工处理
- 没有脏写, 就开始回滚, 回滚方式是使用**前置镜像**, 还原业务数据
- 将保存的镜像删除, 解除行锁



# 项目示例

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

### 使用单机Eureka的版本

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
  instance:
    instance-id: cloud-payment-service8001  # 这里配置微服务名称, 用于在Eureka的监控页面显示
    prefer-ip-address: true                 # 鼠标放在服务名上, 左下角显示服务ip
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://localhost:7001/eureka   # 注册中心地址

# info中配置的信息, 会被/actuator/info这个接口返回
# 在Eureka的监控页面, 点击服务名打开的页面会返回info里面自定义的数据
info:
  # 略
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
  instance:
    instance-id: cloud-payment-service8001  # 这里配置微服务名称, 用于在Eureka的监控页面显示
    prefer-ip-address: true                 # 鼠标放在服务名上, 左下角显示服务ip
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://localhost:7001/eureka   # 注册中心地址
      
# info中配置的信息, 会被/actuator/info这个接口返回
# 在Eureka的监控页面, 点击服务名打开的页面会返回info里面自定义的数据
info:
  # 略
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

### 使用集群Eureka的版本

新建三个Eureka子模块

- **eureka.instance.hostname** 需要不同
- **eureka.client.service-url.defaultZone** 中的host和port需要不同, 因为host为自定义的内容, 所以需要配置到本地host文件中

#### host文件配置

```properties
127.0.0.1       eureka7001.com
127.0.0.1       eureka7002.com
127.0.0.1       eureka7003.com
```

#### Eureka1配置文件

```yml
server:
  port: 7001

spring:
  application:
    name: cloud-eureka-server

eureka:
  instance:
    hostname: eureka7001.com      
  client:
    register-with-eureka: false 
    fetch-registry: false   
    service-url:
      # 将eureka服务注册到另外两个注册中心上
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```

#### Eureka2配置文件

```yml
server:
  port: 7002

spring:
  application:
    name: cloud-eureka-server

eureka:
  instance:
    hostname: eureka7002.com
  client:
    register-with-eureka: false   
    fetch-registry: false       
    service-url:
      # 将eureka服务注册到另外两个注册中心上
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7003.com:7003/eureka/
```

#### Eureka3配置文件

```yml
server:
  port: 7003

spring:
  application:
    name: cloud-eureka-server

eureka:
  instance:
    hostname: eureka7003.com     
  client:
    register-with-eureka: false  
    fetch-registry: false         
    service-url:
      # 将eureka服务注册到另外两个注册中心上
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7003.com:7003/eureka/
```

#### 其他微服务配置

```yml
eureka:
  client:
    register-with-eureka: true  
    fetch-registry: true        
    service-url:
      # 这里需要配置三个注册中心
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```

#### 订单模块

##### RestTemplate开启负载均衡

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced   // 开启负载均衡
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
	
    // restTemplate开启负载均衡后, 在掉其他服务接口时, 只需要指定要调用的服务名cloud-payment-service
    // 默认时以轮询的方式调用
    @GetMapping("/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        return restTemplate.getForObject("http://cloud-payment-service/payment/" + id, CommonResult.class);
    }

    @PostMapping
    public CommonResult add(Payment payment) {
        return restTemplate.postForObject("http://cloud-payment-service/payment", payment, CommonResult.class);
    }
}
```

#### 测试

```properties
# 访问如下三个注册中心的监控页面, 再每个注册中心的监控页面下, 在DS Replicas下能看到另外两个注册中心
# 并且能看到注册进来的每个微服务
http://eureka7001.com:7001/
http://eureka7002.com:7002/
http://eureka7003.com:7003/
```

### 微服务获取注册在Eureka中的其他服务信息

#### 在微服务的Controller中使用

```java
@RequestMapping("/payment")
@RestController
@Slf4j
public class PaymentController {
    @Resource
    private DiscoveryClient discoveryClient;

    @GetMapping("discovery")
    public Object discovery(){
        // 获取服务列表
        List<String> services = discoveryClient.getServices();
        // 获取一个服务下的全部实例
        for (String service : services) {
            List<ServiceInstance> instances = discoveryClient.getInstances(service);
            // 从实例中获取信息
            for (ServiceInstance instance : instances) {
                log.info(instance.getServiceId() + "\t" + instance.getHost() + "\t" + instance.getPort());
            }
        }
        return this.discoveryClient;
    }
}
```

### 使用单机Zookeeper版本

#### 支付模块

##### pom文件

```xml
<dependencies>
    <!-- zookeeper客户端与springcloud整合包 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        <!-- 如果出现zookeeper的安装版本和使用的jar包版本不同意的情况, 排除使用的版本 -->
        <!-- <exclusions>
             <exclusion>
                 <groupId>org.apache.zookeeper</groupId>
              <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions> -->
    </dependency>
    <!-- 手动安装指定版本 -->
    <!--
        <dependency>
         <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version></version>
        </dependency>
  -->
</dependencies>
```

##### 配置文件

```yml
server:
  port: 8003

spring:
  application:
    name:  cloud-payment-service
  # zookeeper配置
  cloud:
    zookeeper:
      # 如果配置集群, 只需要用逗号在后面配置多个localhost:2181,localhost:2182
      connect-string: localhost:2181
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
// 该注解用于向consul和zookeeper作为注册中心时使用注册服务
@EnableDiscoveryClient
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
    <!-- zookeeper客户端与springcloud整合包 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        <!-- 如果出现zookeeper的安装版本和使用的jar包版本不同意的情况, 排除使用的版本 -->
        <!-- <exclusions>
             <exclusion>
                 <groupId>org.apache.zookeeper</groupId>
              <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions> -->
    </dependency>
    <!-- 手动安装指定版本 -->
    <!--
        <dependency>
         <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version></version>
        </dependency>
  -->
</dependencies>
```

##### 配置文件

```yml
spring:
  application:
    name: cloud-order-service
  cloud:
    zookeeper:
      # 如果配置集群, 只需要用逗号在后面配置多个localhost:2181,localhost:2182
      connect-string: localhost:2181
```

##### java配置

```java
@Configuration
@LoadBalanced
public class ApplicationContextConfig {
    // 创建RestTemplate的Bean
    @Bean
    @LoadBalanced
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
// 该注解用于向consul和zookeeper作为注册中心时使用注册服务
@EnableDiscoveryClient
public class OrderMain81 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain81.class, args);
    }
}
```

### 使用单机Consul版本

#### 支付模块

##### pom文件

```xml
<dependencies>
    <!-- consul客户端与springcloud整合包 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>
</dependencies>
```

##### 配置文件

```yml
server:
  port: 8005

spring:
  application:
    name:  cloud-payment-service
  # consul配置
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true  # 开启健康检查
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
// 该注解用于向consul和zookeeper作为注册中心时使用注册服务
@EnableDiscoveryClient
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
    <!-- consul客户端与springcloud整合包 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>
</dependencies>
```

##### 配置文件

```yml
spring:
  application:
    name: cloud-order-service
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true   # 开启健康检查
```

##### java配置

```java
@Configuration
@LoadBalanced
public class ApplicationContextConfig {
    // 创建RestTemplate的Bean
    @Bean
    @LoadBalanced
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
// 该注解用于向consul和zookeeper作为注册中心时使用注册服务
@EnableDiscoveryClient
public class OrderMain81 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain81.class, args);
    }
}
```

### 使用Hystrix

#### 支付模块

##### pom文件

```xml
<dependencies>
    <!-- 增加Hystris包 -->
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependencies>
```

##### 配置文件

```yml
spring:
  application:
    name: cloud-payment-hystrix-service
```

##### java配置

```java
@Configuration
@LoadBalanced
public class ApplicationContextConfig {
    // 创建RestTemplate的Bean
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

##### Controller

```java
@RequestMapping("/payment")
@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @Value("server.port")
    private String port;

    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id")Long id){
        String res = paymentService.paymentInfo_OK(id);
        log.info(res);
        return res;
    }

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_Timeout(@PathVariable("id")Long id){
        String res = paymentService.paymentInfo_Timeout(id);
        log.info(res);
        return res;
    }

}
```

##### Service

```java
@Service
// 设置这个类下, 全部方法默认的服务降级回调方法
@DefaultProperties(defaultFallback = "default_paymentInfo_TimeoutHandler", commandProperties = {
    @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1000")
})
public class PaymentServiceImpl implements PaymentService {

    @Override
    // 当指定全局的服务降级方法, 哪个方法需要使用, 需要加该注解
    @HystrixCommand
    public String paymentInfo_OK(Long id) {
        return "线程池" + Thread.currentThread().getName() + "ok:" + id;
    }

    @Override
    // @HystrixCommand指定超时, 或者出错要回调的方法
    @HystrixCommand(fallbackMethod = "paymentInfo_TimeoutHandler", commandProperties = {
            // 指定超时时间
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
    })
    public String paymentInfo_Timeout(Long id) {
        try {
            TimeUnit.SECONDS.sleep(5);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池" + Thread.currentThread().getName() + "timeout:" + id;
    }
    // 回调的方法
    public String paymentInfo_TimeoutHandler(Long id){
        return "线程池" + Thread.currentThread().getName() + "timeout:" + id + "/(ㄒoㄒ)/~~";
    }
    // 默认的服务降级回调方法
    // 全局的方法, 不能有入参
    public String default_paymentInfo_TimeoutHandler(){
        return "/(ㄒoㄒ)/~~";
    }
}
```

##### 启动类

```java
@SpringBootApplication
@EnableEurekaClient
@@EnableHystrix   // 使用Hystrix
public class PaymentHystrixMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentHystrixMain8001.class, args);
    }
}
```

#### 订单模块(方式1)

在controller中开启服务降级

##### pom文件

```xml
<dependency>
    <!-- 增加Hystris包 -->
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

##### 配置文件

```yml
server:
  port: 80
spring:
  application:
    name: cloud-openfeign-hystrix-order-service

eureka:
  instance:
    instance-id: cloud-openfeign-hystrix-order-service80  # 这里配置微服务名称, 用于在Eureka的监控页面显示
    prefer-ip-address: true             # 鼠标放在服务名上, 左下角显示服务ip
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
 
 # 设置ribbon客户端超时时间
ribbon:
  # 调接口时间
  ReadTimeout: 5000
  # 建立连接时间
  ConnectTimeout: 5000
```

##### Controller

```java
@RestController
@RequestMapping("order")
@Slf4j
// 指定这个类的服务降级的回调方法
@DefaultProperties(defaultFallback = "default_paymentInfo_TimeoutHandler", commandProperties = {
        @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
})
public class OrderController {

    @Resource
    private PaymentOpenFeignService paymentOpenFeignService;

    @GetMapping("/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id")Long id){
        String res = paymentOpenFeignService.paymentInfo_OK(id);
        log.info(res);
        return res;
    }

    @GetMapping("/hystrix/timeout/{id}")
    // 指定服务降级的回调方法
    @HystrixCommand(fallbackMethod = "paymentInfo_TimeoutHandler", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
    })
    public String paymentInfo_Timeout(@PathVariable("id")Long id){
        String res = paymentOpenFeignService.paymentInfo_Timeout(id);
        log.info(res);
        return res;
    }
	
    public String paymentInfo_TimeoutHandler(Long id){
        return "Order线程池" + Thread.currentThread().getName() + "timeout:" + id + "/(ㄒoㄒ)/~~";
    }
    // 全局的降级方法, 是无参
    public String default_paymentInfo_TimeoutHandler(){
        return "/(ㄒoㄒ)/~~";
    }
}
```

##### service

```java
@Service
@FeignClient(value = "CLOUD-PAYMENT-HYSTRIX-SERVICE", contextId = "PaymentOpenFeignService", path = "/payment")
public interface PaymentOpenFeignService {

    @GetMapping("/hystrix/ok/{id}")
    String paymentInfo_OK(@PathVariable("id")Long id);

    @GetMapping("/hystrix/timeout/{id}")
    String paymentInfo_Timeout(@PathVariable("id")Long id);
}
```

##### 启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
@EnableHystrix
public class OpenFeignHystrixOrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OpenFeignHystrixOrderMain80.class, args);
    }
}
```

#### 订单模块(方式2 推荐)

在Feign的服务类中, 开启服务降级, 与Controller解耦, 只适用于消费端

##### pom文件

```xml
<dependency>
    <!-- 增加Hystris包 -->
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

##### 配置文件

```yml
server:
  port: 80
spring:
  application:
    name: cloud-openfeign-hystrix-order-service

eureka:
  instance:
    instance-id: cloud-openfeign-hystrix-order-service80  # 这里配置微服务名称, 用于在Eureka的监控页面显示
    prefer-ip-address: true             # 鼠标放在服务名上, 左下角显示服务ip
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/


# 如果feign.hystrix.enabled = true,  使用注解配置的超时时间会无效
feign:
  hystrix:
    enabled: true
# 将会使用如下配置的时间
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 5000
 
 # 设置ribbon客户端超时时间
ribbon:
  # 调接口时间
  ReadTimeout: 5000
  # 建立连接时间
  ConnectTimeout: 5000
```

##### Controller

```java
@RestController
@RequestMapping("order")
@Slf4j
public class OrderController {

    @Resource
    private PaymentOpenFeignService paymentOpenFeignService;

    @GetMapping("/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id")Long id){
        String res = paymentOpenFeignService.paymentInfo_OK(id);
        log.info(res);
        return res;
    }
    public String paymentInfo_Timeout(@PathVariable("id")Long id){
        String res = paymentOpenFeignService.paymentInfo_Timeout(id);
        log.info(res);
        return res;
    }
}
```

##### service

```java
@Service
@FeignClient(value = "CLOUD-PAYMENT-HYSTRIX-SERVICE", contextId = "PaymentOpenFeignService", path = "/payment", 
             // 指定服务降级类
             fallback = PaymentOpenFeignFallbackService.class)
public interface PaymentOpenFeignService {

    @GetMapping("/hystrix/ok/{id}")
    String paymentInfo_OK(@PathVariable("id")Long id);

    @GetMapping("/hystrix/timeout/{id}")
    String paymentInfo_Timeout(@PathVariable("id")Long id);
}
```

##### 服务降级类

```java
@Component
// 实现要降级的接口
public class PaymentOpenFeignFallbackService implements PaymentOpenFeignService{
    @Override
    public String paymentInfo_OK(Long id) {
        return "ok 超时了";
    }

    @Override
    public String paymentInfo_Timeout(Long id) {
        return "timeout 超时了";
    }
}
```

##### 启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
@EnableHystrix
public class OpenFeignHystrixOrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OpenFeignHystrixOrderMain80.class, args);
    }
}
```

### 使用Gateway

#### pom文件

```xml
<!-- 注： 不能加spring-boot-starter-web 和 spring-boot-starter-actuator 否则无法启动项目 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

#### 配置文件

```yml
server:
  port: 9527
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh                      # 路由的ID， 没有固定的规则， 但是要求唯一， 建议配合服务名
          uri: http://localhost:8001             # 匹配后的提供服务的路由地址
          predicates:
            - Path=/payment/get/**               # 断言， 路径相匹配的进行路由
        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/timeout/**

eureka:
  instance:
    instance-id: cloud-gateway-service9527
  client:
    register-with-eureka: true  # 表示是否将服务注册到Eureka Server中, 默认为true
    fetch-registry: true        # 是否从EurekaServer抓取已有的注册信息, 默认为true, 集群必须设置为true才能使用ribbon进行负载均衡, 单节点可以为false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

#### 启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class GateWayMain9527 {
    public static void main(String[] args) {
        SpringApplication.run(GateWayMain9527.class, args);
    }
}
```

### 使用Config配置中心

#### 服务端

##### pom文件

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

##### 配置文件

```yml
server:
  port: 3344

spring:
  application:
    name: cloud-config-center
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/ITianerU/configuration_center.git      # 创建的git仓库的地址
          search-paths:
            - springcloud-config     # 搜索的仓库的目录, 里面创建consumer-dev.properties
          password: 密码
          username: 用户名
      label: master             # 指定分支

eureka:
  instance:
    instance-id: cloud-config-center3344  # 这里配置微服务名称, 用于在Eureka的监控页面显示
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

##### 启动类

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigCenterMain3344 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigCenterMain3344.class, args);
    }
}
```

##### 测试

```bash
http://localhost:3344/master/config-dev.yml
```

#### 客户端

##### pom文件

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

##### 配置文件

bootstrap.yml

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    config:
      label: master   # 分支名称
      name: config    # 配置文件名称
      profile: dev    # 环境后缀
      uri: http://localhost:3344    # 配置中心地址

eureka:
  instance:
    instance-id: config-client3355  # 这里配置微服务名称, 用于在Eureka的监控页面显示
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
      
# 暴露监控端点, 用于动态刷新
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

##### 启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class ConfigClientMain3355 {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClientMain3355.class, args);
    }
}
```

##### controller

```java
@RefreshScope    // 开启动态刷新注解
@RestController
@RequestMapping("config")
public class ConfigClientController {
    // 从配置中心获取的配置文件中注入
    @Value("${hello}")
    private String msg;

    @GetMapping
    public String getConfigInfo(){
        return msg;
    }
}
```

##### 测试

```bash
http://localhost:3355/config
```

##### 手动触发动态更新

```bash
# 使用post请求
curl -X POST "http://localhost:3355/actuator/refresh"
```

#### 使用消息总线

##### 服务端

###### 依赖

```xml
<!-- 消息总线依赖, 内部整合了RabbitMQ的支持 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

###### 配置文件

```yml
# rabbitmq相关配置
spring:
  rabbitmq:
    host: locahost
    port: 5672
    username: root
    password: 123456
# 暴露bus刷新配置的端点
management:
  endpoints:
    web:
      exposure:
        include: "bus-refresh"
```

##### 客户端

###### 依赖

```xml
<!-- 消息总线依赖, 内部整合了RabbitMQ的支持 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

###### 配置文件

```yml
# rabbitmq相关配置
spring:
  rabbitmq:
    host: locahost
    port: 5672
    username: root
    password: 123456
```

##### 触发服务端更新

###### 广播模式

通知全部服务端

```bash
curl -X POST "http://localhost:3344/actuator/bus-refresh"
```

###### 定点通知

通知指定服务器

```bash
curl -X POST "http://localhost:3344/actuator/bus-refresh/服务名:端口号"
```

### 使用Stream消息驱动

#### 生产者

##### 依赖

```xml
<!-- rabbit整合stream -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
<!-- kafka整合stream -->
<!--        <dependency>-->
<!--            <groupId>org.springframework.cloud</groupId>-->
<!--            <artifactId>spring-cloud-starter-stream-kafka</artifactId>-->
<!--        </dependency>-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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
```

##### 配置文件

```yml
server:
  port: 8801
spring:
  application:
    name: rabbitmq-provider
  rabbitmq:
    host: localhost
    port: 
    username: root
    password: 123456
  cloud:
    stream:
      binders:              # 在此处配置要绑定的rabbitmq的服务信息
        defaultRabbit1:     # 表示子定义的名称, 用于binding整合吗可以有多个
          type: rabbit      # 消息组件类型
        defaultRabbit2:
          type: rabbit
      bindings:             # 服务的整合处理
        output1:            # 这个名字是一个通道的名称, 自定义的名称, 可以有多个
          destination: itianeru1Exchange          # 表示要使用的Exchange名称定义 类似topic
          content-type: application/json          # 消息类型, 文本则要设置"text/plain"
          binder: defaultRabbit1                  # 这里使用的是binders下面的自定义binder
        output2:
          destination: itianeru2Exchange
          content-type: application/json
          binder: defaultRabbit2
eureka:
  instance:
    instance-id: rabbitmq-provider8001  # 这里配置微服务名称, 用于在Eureka的监控页面显示
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/

```

##### Service

```java
// 配置通道的接口
@Component
public interface OutputInterface {
    // 指定选择通道output1
    @Output("output1")
    MessageChannel output1();
	// 指定选择通道output2
    @Output("output2")
    MessageChannel output2();
}

// 接口
public interface IMessageProvider {
    String send1();
    String send2();
}

//  实现类
@EnableBinding(OutputInterface.class)   // 定义消息的推送管道
public class MessageProviderImpl implements IMessageProvider {
	
    // 导入通道选择器
    @Resource
    private OutputInterface outputInterface;

    @Override
    public String send1() {
        String uuid = UUID.randomUUID().toString();
        boolean send = outputInterface.output1().send(MessageBuilder.withPayload(uuid).build());
        System.out.println(send);
        return uuid;
    }
    @Override
    public String send2() {
        String uuid = UUID.randomUUID().toString();
        boolean send = outputInterface.output2().send(MessageBuilder.withPayload(uuid).build());
        System.out.println(send);
        return uuid;
    }
}
```

##### controller

```java
@RestController
@RequestMapping("stream")
public class SendMessageController {

    @Resource
    private IMessageProvider messageProvider;

    @GetMapping("send1")
    public String sendMessage1(){
        return messageProvider.send1();
    }

    @GetMapping("send2")
    public String sendMessage2(){
        return messageProvider.send2();
    }
}
```

#### 消费者

##### 依赖

同生产者一样

##### 配置文件

```yml
server:
  port: 8802
spring:
  application:
    name: rabbitmq-consumer
  rabbitmq:
    host: localhost
    port:
    username: root
    password: 123456
  cloud:
    stream:
      binders:              
        defaultRabbit1:     
          type: rabbit      
        defaultRabbit2:
          type: rabbit
      bindings:             
        input1:                              # 消费的通道
          destination: itianeru1Exchange     # 消费的topic, 要和生产的一直
          content-type: application/json         
          binder: defaultRabbit1      
          group: itianeru1                   # 指定分组, 如果不指定, 默认每个服务都独自一组
        input2:
          destination: itianeru2Exchange
          content-type: application/json
          binder: defaultRabbit2
          group: itianeru1
eureka:
  instance:
    instance-id: rabbitmq-consumer8002  
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

##### Service

```java
// 配置通道的接口
@Component
public interface InputInterface {

    String INPUT1 = "input1";
    String INPUT2 = "input2";

    @Input(INPUT1)
    MessageChannel input1();

    @Input(INPUT2)
    MessageChannel input2();
}

// 接收消息的结构
public interface IMessageConsumer {
    void receive1(Message<String> message);
    void receive2(Message<String> message);
}

@EnableBinding(InputInterface.class)   // 绑定消息的接收管道
public class MessageConsumerImpl implements IMessageConsumer {

    @Value("${server.port}")
    private String port;

    @Override
    @StreamListener(InputInterface.INPUT1)  // 开启监听器, 并指定接收哪个个topic
    public void receive1(Message<String> message) {
        System.out.println("receive1" + message.getPayload() + ":" + port);
    }
    @Override
    @StreamListener(InputInterface.INPUT2)
    public void receive2(Message<String> message) {
        System.out.println("receive2" + message.getPayload() + ":" + port);
    }
}
```

### 使用Sleuth链路追踪

#### 生产者

##### 依赖

```xml
<!-- 添加依赖 链路追踪 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

##### 配置文件

```yml
server:
  port: 80
spring:
  application:
    name:  cloud-payment-service
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      probability: 1   # 采样率值, 介于0-1, 1表示全部采集, 常用0.5
```

##### controller

```java
@RequestMapping("/payment")
@RestController
@Slf4j
public class PaymentController {

    @GetMapping("zipkin")
    public String zipkin(){
        return "zipkin";
    }
}
```

#### 消费者

##### 依赖

同生产者一样

##### 配置文件

```yml
server:
  port: 8001
spring:
  application:
    name:  cloud-payment-service
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      probability: 1   # 采样率值, 介于0-1, 1表示全部采集, 常用0.5
```

##### controller

```java
@RestController
@RequestMapping("order")
public class OrderController {
    @GetMapping("zipkin")
    public String zipkin(){
        return restTemplate.getForObject("http://cloud-payment-service/payment/zipkin", String.class);
    }
}
```

# alibaba版本示例

## 父项目

### pom文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itianeru</groupId>
    <artifactId>SpringCloudAlibabaDemo</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>cloudalibaba-provider-payment</module>
        <module>cloudalibaba-consumer-order</module>
    </modules>

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
            <!-- spring cloud alibaba 2.2.6 -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.2.6.RELEASE</version>
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
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.3.12.RELEASE</version>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## 使用nacos作为注册中心

### 生产者

#### pom文件

```xml
<!-- 使用nacos -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

#### 配置文件

```yml
server:
  port: 9001

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848   # nacos地址

# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

#### 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosPaymentMain {

    public static void main(String[] args) {
        SpringApplication.run(NacosPaymentMain.class, args);
    }
}
```

#### controller

```java
@RestController
@RequestMapping("payment")
public class PaymentController {

    @Value("${server.port}")
    private String port;

    @GetMapping("{id}")
    public String getPayment(@PathVariable("id")Integer id){
        return String.format("nacos registry, serverPort%s:%d", port, id);
    }
}
```

### 消费者

#### pom文件

```xml
<!-- 略, 同生产者 -->
```

#### 配置文件

```yml
server:
  port: 80

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848   # nacos地址

# 消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-payment-service: http://nacos-payment-provider
```

#### 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosOrderMain {

    public static void main(String[] args) {
        SpringApplication.run(NacosOrderMain.class, args);
    }
}
```

#### 配置类

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

#### controller

```java
@RestController
@RequestMapping("order")
public class OrderController {

    @Resource
    private RestTemplate restTemplate;

    @Value("${service-url.nacos-payment-service}")
    private String paymentServiceUrl;

    @GetMapping("{id}")
    public String getPayment(@PathVariable("id")Integer id){
        return restTemplate.getForObject(paymentServiceUrl + "/payment/" + id, String.class);
    }
}
```

## 使用nacos作为配置中心

### 配置中心客户端

#### pom文件

```xml
<dependencies>
    <!-- 配置中心  -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    </dependency>
    <!-- 服务注册发现 -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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

#### 配置文件

##### bootstrap.yml

```yml
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848   # Nacos作为注册中心的地址
      config:
        server-addr: localhost:8848   # Nacos作为配置中心的地址
        file-extension: yaml          # 指定yaml格式的配置, 不能指定为yml
```

##### application.yml

```yml
spring:
  profiles:
    active: dev  # 表示要从配置中心拉取什么环境的配置文件
```

#### 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosConfigClientMain {

    public static void main(String[] args) {
        SpringApplication.run(NacosConfigClientMain.class, args);
    }
}
```

#### 配置类

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

#### controller

```java
@RestController
@RefreshScope  // 支持Nacos的动态刷新功能
@RequestMapping("config")
public class ConfigClientController {

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("info")
    public String info(){
        return configInfo;
    }
}
```

## 使用sentinel做服务降级

### 配置中心客户端

#### pom文件

```xml
<dependencies>
    <!-- 配置中心  -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    </dependency>
    <!-- 服务注册发现 -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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

#### 配置文件

##### bootstrap.yml

```yml
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848   # Nacos作为注册中心的地址
      config:
        server-addr: localhost:8848   # Nacos作为配置中心的地址
        file-extension: yaml          # 指定yaml格式的配置, 不能指定为yml
```

##### application.yml

```yml
spring:
  profiles:
    active: dev  # 表示要从配置中心拉取什么环境的配置文件
```

#### 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosConfigClientMain {

    public static void main(String[] args) {
        SpringApplication.run(NacosConfigClientMain.class, args);
    }
}
```

#### 配置类

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

#### controller

```java
@RestController
@RefreshScope  // 支持Nacos的动态刷新功能
@RequestMapping("config")
public class ConfigClientController {

    @Value("${config.ingo}")
    private String configInfo;

    @GetMapping("info")
    public String info(){
        return configInfo;
    }
}
```

## sentinel整合openfeign

### 消费者

#### 依赖

```xml
<dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <!-- sentinel持久化 -->
        <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
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
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    	<!-- 热部署插件可能导致服务无法启动 -->
        <!-- <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency> -->
    </dependencies>
```

#### 配置文件

```yml
server:
  port: 80

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8851   # nacos地址
    sentinel:
      transport:
        dashboard: localhost:8858 # sentinel监控地址
        port: 8719   # 默认端口, 假如被占用会自动从8719开始依次+1扫描, 直到找到未被占用的端口

# 消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-payment-service: http://nacos-payment-provider

# 开启feign对sentinel的支持
feign:
  sentinel:
    enabled: true
```

#### 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class NacosOrderMain {

    public static void main(String[] args) {
        SpringApplication.run(NacosOrderMain.class, args);
    }
}
```

#### 服务

```java
@FeignClient(value = "nacos-payment-provider", path = "/payment" ,fallback = PaymentFallbackService.class)
public interface PaymentService {
    @GetMapping("{id}")
    public String getPayment(@PathVariable("id")Integer id);
}
```

#### 异常处理器

```java
@Component
public class PaymentFallbackService implements PaymentService{

    @Override
    public String getPayment(Integer id){
        return "错误了";
    }
}
```

#### controller

```java
@RestController
@RequestMapping("order")
public class OrderController {

    @Resource
    private PaymentService paymentService;

    @GetMapping("{id}")
    public String getPayment(@PathVariable("id")Integer id){
        return paymentService.getPayment(id);
    }

}
```

## 使用Seata

### 安装

```text
请查看docker笔记
```

### 依赖

```xml
<dependencies>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
        <!--            <exclusions>-->
        <!--                <exclusion>-->
        <!--                    <groupId>io.seata</groupId>-->
        <!--                    <artifactId>seata-all</artifactId>-->
        <!--                </exclusion>-->
        <!--            </exclusions>-->
    </dependency>
    <!--        <dependency>-->
    <!--            <groupId>io.seata</groupId>-->
    <!--            <artifactId>seata-all</artifactId>-->
    <!--            <version>1.4.2</version>-->
    <!--        </dependency>-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
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
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>com.itianeru</groupId>
        <artifactId>cloudalibaba-api-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

### 订单服务

#### conf文件

##### file.conf

```properties
store{
  # 事务日志存储模块, 有file和db两种
  # mode="file"

#   file{
#     dir="sessionStore"
#   }
  # 这里使用数据库模式
    mode="db"

    db {
        ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
        datasource = "seata"
        ## mysql/oracle/postgresql/h2/oceanbase etc.
        dbType = "mysql"
        driverClassName = "com.mysql.cj.jdbc.Driver"
        url = "jdbc:mysql://localhost:3307/seata"
        user = "root"
        password = "123456"
        minConn = 5
        maxConn = 30
        globalTable = "global_table"
        branchTable = "branch_table"
        lockTable = "lock_table"
        queryLimit = 100
        maxWait = 5000
    }
}


transport {
  # tcp udt unix-domain-socket
  type = "TCP"
  #NIO NATIVE
  server = "NIO"
  #enable heartbeat
  heartbeat = true
  # the client batch send request enable
  enableClientBatchSendRequest = true
  #thread factory for netty
  threadFactory {
    bossThreadPrefix = "NettyBoss"
    workerThreadPrefix = "NettyServerNIOWorker"
    serverExecutorThread-prefix = "NettyServerBizHandler"
    shareBossWorker = false
    clientSelectorThreadPrefix = "NettyClientSelector"
    clientSelectorThreadSize = 1
    clientWorkerThreadPrefix = "NettyClientWorkerThread"
    # netty boss thread size,will not be used for UDT
    bossThreadSize = 1
    #auto default pin or 8
    workerThreadSize = "default"
  }
  shutdown {
    # when destroy server, wait seconds
    wait = 3
  }
  serialization = "seata"
  compressor = "none"
}

service {
  #transaction service group mapping
  vgroupMapping.my_test_tx_group = "default"
  #only support when registry.type=file, please don't set multiple addresses
  default.grouplist = "127.0.0.1:8091"
  #degrade, current not support
  enableDegrade = false
  #disable seata
  disableGlobalTransaction = false
}

client {
  rm {
    asyncCommitBufferLimit = 10000
    lock {
      retryInterval = 10
      retryTimes = 30
      retryPolicyBranchRollbackOnConflict = true
    }
    reportRetryCount = 5
    tableMetaCheckEnable = false
    reportSuccessEnable = false
  }
  tm {
    commitRetryCount = 5
    rollbackRetryCount = 5
  }
  undo {
    dataValidation = true
    logSerialization = "jackson"
    logTable = "undo_log"
  }
  log {
    exceptionRate = 100
  }
}
```

##### registry.conf

```properties
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "localhost:8851"
    group = "DEFAULT_GROUP"
    cluster = "default"
    namespace = ""
    username = "nacos"
    password = "nacos"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = "0"
    password = ""
    timeout = "0"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

# config{
#
#   type = "file"
#
#   file{
#   # 容器内部内部配置文件的位置
#     name = "file:/root/seata-config/file.conf"
#   }
# }

config {
  # file、nacos 、apollo、zk、consul、etcd3、springCloudConfig
  type = "file"

  nacos {
    serverAddr = "localhost"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file:/seata-server/resources/file.conf"
  }
}
```

#### application.yml

```yml
server:
  port: 2001
spring:
  application:
    name: seata-order-service
  cloud:
    alibaba:
      seata:
        tx-service-group: my_test_tx_group
    nacos:
      discovery:
        server-addr: localhost:8851
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3307/seata_order
    username: root
    password: 123456


feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.itianeru.alibabaspringcloud.domain
```

#### Java配置

##### DataSourceProxyConfig

```java
@Configuration
public class DataSourceProxyConfig {

    @Value("${mybatis.mapper-locations}")
    private String mapperLocations;

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }
    // 这里是seata的DataSourceProxy
    @Bean
    public DataSourceProxy dataSourceProxy(DataSource dataSource){
        return new DataSourceProxy(dataSource);
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception{
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSourceProxy);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
        sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
        return sqlSessionFactoryBean.getObject();
    }
}
```

##### MyBatisConfig

```java
@Configuration
@MapperScan({"com.itianeru.alibabaspringcloud.dao"})
public class MyBatisConfig {
}
```

#### domain

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Order {
    private Long id;
    private Long userId;
    private Long productId;
    private Integer count;
    private BigDecimal money;
    private Integer status;
}
```

#### dao

```java
@Mapper
public interface OrderDao {

    void create(Order order);
    void update(@Param("userId") Long userId, @Param("status") Integer status);
}
```

#### mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.itianeru.alibabaspringcloud.dao.OrderDao">
    <insert id="create" parameterType="com.itianeru.alibabaspringcloud.domain.Order" useGeneratedKeys="true" keyProperty="id">
       insert into t_order (id, user_id,product_id, count, money, status)
       values(null, #{userId}, #{productId}, #{count}, #{money}, 0)
    </insert>
    <update id="update">
        update t_order set status = 1 where user_id = #{userId} and status = #{status};
    </update>
</mapper>
```

#### service

##### AccountService

```java
@FeignClient(value = "seata-account-service", path = "/account")
public interface AccountService {

    @PostMapping("decrease")
    CommonResult decrease(@RequestParam("userId")Long productId, @RequestParam("money")BigDecimal money);
}
```

##### OrderService

```java
public interface OrderService {
    void create(Order order);
}
```

###### OrderServiceImpl

```java
@Service
@Slf4j
public class OrderServiceImpl implements OrderService {

    @Resource
    private OrderDao orderDao;

    @Resource
    private AccountService accountService;

    @Resource
    private StorageService storageService;

    @Override
    // name 是唯一值, rollbackFor指定出现什么异常就全局回滚
    @GlobalTransactional(name = "my_test_tx_group" ,rollbackFor = Exception.class)
    public void create(Order order) {
        log.info("==============开始创建新订单");
        orderDao.create(order);

        log.info("==============订单服务开始调用库存, 做扣减");
        storageService.decrease(order.getProductId(), order.getCount());
        log.info("==============订单服务开始调用库存, 做扣减 ending");

        log.info("==============订单服务开始调用库账户, 做扣减");
        accountService.decrease(order.getUserId(), order.getMoney());
        log.info("==============订单服务开始调用库账户, 做扣减 ending");

        log.info("==============开始修改订单状态");
        orderDao.update(order.getUserId(), 0);
        log.info("==============修改订单状态结束");

        log.info("==============下订单结束");
    }
}
```

##### StorageService

```java
@FeignClient(value = "seata-storage-service", path = "/storage")
public interface StorageService {

    @PostMapping("decrease")
    CommonResult decrease(@RequestParam("productId")Long productId, @RequestParam("count") Integer count);
}
```

#### controller

```java
@RestController
@RequestMapping("order")
public class OrderController {

    @Resource
    private OrderService orderService;

    @GetMapping("create")
    public CommonResult create(Order order){
        orderService.create(order);
        return new CommonResult(200, "订单创建成功");
    }
}
```

#### 启动类

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataOrderMain {
    public static void main(String[] args) {
        SpringApplication.run(SeataOrderMain.class, args);
    }
}
```

### 商品服务

#### conf文件

##### file.conf

```properties
store{
  # 事务日志存储模块, 有file和db两种
  # mode="file"

#   file{
#     dir="sessionStore"
#   }
  # 这里使用数据库模式
    mode="db"

    db {
        ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
        datasource = "seata"
        ## mysql/oracle/postgresql/h2/oceanbase etc.
        dbType = "mysql"
        driverClassName = "com.mysql.cj.jdbc.Driver"
        url = "jdbc:mysql://localhost:3307/seata"
        user = "root"
        password = "123456"
        minConn = 5
        maxConn = 30
        globalTable = "global_table"
        branchTable = "branch_table"
        lockTable = "lock_table"
        queryLimit = 100
        maxWait = 5000
    }
}


transport {
  # tcp udt unix-domain-socket
  type = "TCP"
  #NIO NATIVE
  server = "NIO"
  #enable heartbeat
  heartbeat = true
  # the client batch send request enable
  enableClientBatchSendRequest = true
  #thread factory for netty
  threadFactory {
    bossThreadPrefix = "NettyBoss"
    workerThreadPrefix = "NettyServerNIOWorker"
    serverExecutorThread-prefix = "NettyServerBizHandler"
    shareBossWorker = false
    clientSelectorThreadPrefix = "NettyClientSelector"
    clientSelectorThreadSize = 1
    clientWorkerThreadPrefix = "NettyClientWorkerThread"
    # netty boss thread size,will not be used for UDT
    bossThreadSize = 1
    #auto default pin or 8
    workerThreadSize = "default"
  }
  shutdown {
    # when destroy server, wait seconds
    wait = 3
  }
  serialization = "seata"
  compressor = "none"
}

service {
  #transaction service group mapping
  vgroupMapping.my_test_tx_group = "default"
  #only support when registry.type=file, please don't set multiple addresses
  default.grouplist = "127.0.0.1:8091"
  #degrade, current not support
  enableDegrade = false
  #disable seata
  disableGlobalTransaction = false
}

client {
  rm {
    asyncCommitBufferLimit = 10000
    lock {
      retryInterval = 10
      retryTimes = 30
      retryPolicyBranchRollbackOnConflict = true
    }
    reportRetryCount = 5
    tableMetaCheckEnable = false
    reportSuccessEnable = false
  }
  tm {
    commitRetryCount = 5
    rollbackRetryCount = 5
  }
  undo {
    dataValidation = true
    logSerialization = "jackson"
    logTable = "undo_log"
  }
  log {
    exceptionRate = 100
  }
}
```

##### registry.conf

```properties
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "localhost:8851"
    group = "DEFAULT_GROUP"
    cluster = "default"
    namespace = ""
    username = "nacos"
    password = "nacos"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = "0"
    password = ""
    timeout = "0"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

# config{
#
#   type = "file"
#
#   file{
#   # 容器内部内部配置文件的位置
#     name = "file:/root/seata-config/file.conf"
#   }
# }

config {
  # file、nacos 、apollo、zk、consul、etcd3、springCloudConfig
  type = "file"

  nacos {
    serverAddr = "localhost"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file:/seata-server/resources/file.conf"
  }
}
```

#### application.yml

```yml
server:
  port: 2002
spring:
  application:
    name: seata-order-service
  cloud:
    alibaba:
      seata:
        tx-service-group: my_test_tx_group
    nacos:
      discovery:
        server-addr: localhost:8851
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3307/seata_storage
    username: root
    password: 123456


feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.itianeru.alibabaspringcloud.domain
```

#### Java配置

##### DataSourceProxyConfig

```java
@Configuration
public class DataSourceProxyConfig {

    @Value("${mybatis.mapper-locations}")
    private String mapperLocations;

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }
    // 这里是seata的DataSourceProxy
    @Bean
    public DataSourceProxy dataSourceProxy(DataSource dataSource){
        return new DataSourceProxy(dataSource);
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception{
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSourceProxy);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
        sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
        return sqlSessionFactoryBean.getObject();
    }
}
```

##### MyBatisConfig

```java
@Configuration
@MapperScan({"com.itianeru.alibabaspringcloud.dao"})
public class MyBatisConfig {
}
```

#### domain

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Storage {

    private Long id;
    private Long productId;
    private Integer total;
    private Integer used;
    private Integer residue;
}
```

#### dao

```java
@Mapper
public interface StorageDao {
    void decrease(@Param("productId") Long productId, @Param("count") Integer count);
}
```

#### mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.itianeru.alibabaspringcloud.dao.StorageDao">
    <update id="decrease">
        update t_storage
        set used = used + #{count}, residue = residue - #{count}
        where product_id = #{productId}
    </update>
</mapper>
```

#### service

##### AccountService

```java
@Service
public interface StorageService {
    void decrease(Long productId, Integer count);
}
```

#### controller

```java
@RestController
@RequestMapping("storage")
public class StorageController {

    @Resource
    private StorageService storageService;

    @PostMapping("decrease")
    public CommonResult decrease(Long productId, Integer count){
        storageService.decrease(productId, count);
        return new CommonResult(200, "扣减库存成功");
    }
}
```

#### 启动类

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataStorageMain {
    public static void main(String[] args) {
        SpringApplication.run(SeataStorageMain.class, args);
    }
}
```

### 账户服务

#### conf文件

##### file.conf

```properties
store{
  # 事务日志存储模块, 有file和db两种
  # mode="file"

#   file{
#     dir="sessionStore"
#   }
  # 这里使用数据库模式
    mode="db"

    db {
        ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
        datasource = "seata"
        ## mysql/oracle/postgresql/h2/oceanbase etc.
        dbType = "mysql"
        driverClassName = "com.mysql.cj.jdbc.Driver"
        url = "jdbc:mysql://localhost:3307/seata"
        user = "root"
        password = "123456"
        minConn = 5
        maxConn = 30
        globalTable = "global_table"
        branchTable = "branch_table"
        lockTable = "lock_table"
        queryLimit = 100
        maxWait = 5000
    }
}


transport {
  # tcp udt unix-domain-socket
  type = "TCP"
  #NIO NATIVE
  server = "NIO"
  #enable heartbeat
  heartbeat = true
  # the client batch send request enable
  enableClientBatchSendRequest = true
  #thread factory for netty
  threadFactory {
    bossThreadPrefix = "NettyBoss"
    workerThreadPrefix = "NettyServerNIOWorker"
    serverExecutorThread-prefix = "NettyServerBizHandler"
    shareBossWorker = false
    clientSelectorThreadPrefix = "NettyClientSelector"
    clientSelectorThreadSize = 1
    clientWorkerThreadPrefix = "NettyClientWorkerThread"
    # netty boss thread size,will not be used for UDT
    bossThreadSize = 1
    #auto default pin or 8
    workerThreadSize = "default"
  }
  shutdown {
    # when destroy server, wait seconds
    wait = 3
  }
  serialization = "seata"
  compressor = "none"
}

service {
  #transaction service group mapping
  vgroupMapping.my_test_tx_group = "default"
  #only support when registry.type=file, please don't set multiple addresses
  default.grouplist = "127.0.0.1:8091"
  #degrade, current not support
  enableDegrade = false
  #disable seata
  disableGlobalTransaction = false
}

client {
  rm {
    asyncCommitBufferLimit = 10000
    lock {
      retryInterval = 10
      retryTimes = 30
      retryPolicyBranchRollbackOnConflict = true
    }
    reportRetryCount = 5
    tableMetaCheckEnable = false
    reportSuccessEnable = false
  }
  tm {
    commitRetryCount = 5
    rollbackRetryCount = 5
  }
  undo {
    dataValidation = true
    logSerialization = "jackson"
    logTable = "undo_log"
  }
  log {
    exceptionRate = 100
  }
}
```

##### registry.conf

```properties
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "localhost:8851"
    group = "DEFAULT_GROUP"
    cluster = "default"
    namespace = ""
    username = "nacos"
    password = "nacos"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = "0"
    password = ""
    timeout = "0"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

# config{
#
#   type = "file"
#
#   file{
#   # 容器内部内部配置文件的位置
#     name = "file:/root/seata-config/file.conf"
#   }
# }

config {
  # file、nacos 、apollo、zk、consul、etcd3、springCloudConfig
  type = "file"

  nacos {
    serverAddr = "localhost"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file:/seata-server/resources/file.conf"
  }
}
```

#### application.yml

```yml
server:
  port: 2003
spring:
  application:
    name: seata-order-service
  cloud:
    alibaba:
      seata:
        tx-service-group: my_test_tx_group
    nacos:
      discovery:
        server-addr: localhost:8851
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3307/seata_account
    username: root
    password: 123456


feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.itianeru.alibabaspringcloud.domain
```

#### Java配置

##### DataSourceProxyConfig

```java
@Configuration
public class DataSourceProxyConfig {

    @Value("${mybatis.mapper-locations}")
    private String mapperLocations;

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }
    // 这里是seata的DataSourceProxy
    @Bean
    public DataSourceProxy dataSourceProxy(DataSource dataSource){
        return new DataSourceProxy(dataSource);
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception{
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSourceProxy);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
        sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
        return sqlSessionFactoryBean.getObject();
    }
}
```

##### MyBatisConfig

```java
@Configuration
@MapperScan({"com.itianeru.alibabaspringcloud.dao"})
public class MyBatisConfig {
}
```

#### domain

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Account {
    private Long id;
    private Long userId;
    private Integer total;
    private Integer used;
    private Integer residue;
}
```

#### dao

```java
@Mapper
public interface AccountDao {
    void decrease(@Param("userId") Long userId, @Param("money")BigDecimal money);
}
```

#### mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.itianeru.alibabaspringcloud.dao.AccountDao">
    <update id="decrease">
        update t_account
        set used = used + #{money}, residue = residue - #{money}
        where user_id = #{userId}
    </update>
</mapper>
```

#### service

##### AccountService

```java
@Service
public interface AccountService {
    void decrease(Long userId, BigDecimal money);
}
```

#### controller

```java
@RestController
@RequestMapping("account")
public class AccountController {

    @Resource
    private AccountService accountService;

    @PostMapping ("decrease")
    public CommonResult update(Long userId, BigDecimal money){
        accountService.decrease(userId, money);
        return new CommonResult(200, "扣减账户成功");
    }
}
```

#### 启动类

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataAccountMain {
    public static void main(String[] args) {
        SpringApplication.run(SeataAccountMain.class, args);
    }
}
```

# 搭建Nacos集群

需要nginx + nacos + mysql

请看docker笔记  
