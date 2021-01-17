# 学习网站

- https://springcloud.cc/spring-cloud-netflix.html
- 中文API   https://springcloud.cc/spring-cloud-dalston.html
- 社区 http://springcloud.cn/
- 中文网  http://springcloud.cc

# 项目搭建

## 父项目

### pom

```xml
<groupId>com.itianeru</groupId>
<artifactId>springcloud</artifactId>
<version>1.0</version>
<modules>
	<module>springcloud-api</module>
</modules>
<packaging>pom</packaging>
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <junit.version>4.12</junit.version>
    <lombok.version>1.16.10</lombok.version>
    <log4j.version>1.2.17</log4j.version>
    <springcloud.version>Greenwich.SR1</springcloud.version>
    <springcloud.component.version>1.4.6.RELEASE</springcloud.component.version>
    <springboot.version>2.1.4.RELEASE</springboot.version>
    <springcloud-api.version>1.0</springcloud-api.version>
    <mysql.version>5.1.47</mysql.version>
    <druid.version>1.1.10</druid.version>
    <mybatis.version>1.3.2</mybatis.version>
</properties>

<dependencyManagement>
    <dependencies>
        <!-- 实体类 -->
        <dependency>
            <groupId>com.itianeru</groupId>
            <artifactId>springcloud-api</artifactId>
            <springcloud-api.version>${springcloud-api.version}</springcloud-api.version>
        </dependency>
        <!-- eureka 注册中心 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
            <version>${springcloud.component.version}</version>
        </dependency>
        <!-- eureka 客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
            <version>${springcloud.component.version}</version>
        </dependency>
        <!-- springcloud -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <!-- 该版本号是单词首字母 A B C D 这样排列-->
            <version>${springcloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!-- ribbon负载均衡 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-ribbon</artifactId>
            <version>${springcloud.component.version}</version>
        </dependency>
        <!-- feign -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-feign</artifactId>
            <version>${springcloud.component.version}</version>
        </dependency>
        <!-- feign的依赖中包含Hystrix, 如果不使用feign需要再单独引入 -->
        <!-- <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix</artifactId>
            <version>${springcloud.component.version}</version>
        </dependency> -->
        <!-- springboot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${springboot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!-- 数据库 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>
        <!-- druid -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid.version}</version>
        </dependency>
        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
        <!-- junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
        </dependency>
        <!-- lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
        </dependency>
        <!-- log4j -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## 注册中心

### 对比

**CAP原则:**  C 一致性,  A 可用性, P 容错性, 在分布式系统中, P容错性是不可或缺的, 因此只能在A和C中权衡

- **Zookeeper:**  保证了CP

  当向注册中心查询接口服务, 我们可以容忍注册中心返回的是几分钟之前的注册信息, 但不能接受服务直接宕掉不可用, 就是降低A可用性, 提高C一致性,  但是zk的master节点如果宕掉, 其他节点会重新选举leader, 但是选举的时间过长, 30-120s(**不一定是真的, 没查到**), 选举期间, 整个zk集群都是不可用的, 导致注册服务瘫痪, 这是不能容忍的 

- **Eureka:**  保证了AP

  Eureka优先保证可用性, A大于C, 各个节点都是平等的, 单个节点挂掉, 不影响其他节点,  服务注册时, 如果发现连接失败, 会切换节点进行注册, 只要有一台Eureka服务可用, 就能保证可用性, 但是查到的信息可能不是最新的, 一致性差, 另外Eureka有自我保护机制, 如果15分钟内超过85%的节点没有正常的心跳, 那么Eureka会认为网络出现故障, 会出现这几种状况

  - Eureka不再从注册列表中移除长时间没有心跳的服务
  - Eureka仍然能够接受新的服务注册和查询, 但是不会同步到其他节点
  - 当网络稳定时, 再将信息同步

### Eureka

#### 依赖

```xml
<parent>
    <groupId>com.itianeru</groupId>
    <artifactId>springcloud</artifactId>
    <version>1.0</version>
</parent>
<modelVersion>4.0.0</modelVersion>
<artifactId>springcloud-eureka</artifactId>

<packaging>jar</packaging>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-erueka-server</artifactId>
    </dependency>
    <!-- 热部署工具 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

#### 配置

```yml
server:
	port: 7001

eureka:
	instance:
		hostname: localhost  # eureka 服务端实例名称
	client:
		register-with-erueka: false  # 表示是否向eureka注册自己 集群配置使用
		fetch-registtry: false # 如果为false 则表示自己为注册中心
		service-url: 
			defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/   # 注册中心地址
	server:
		enable-preservation: false # 关闭自我保护模式
```

#### 启动类注解

```java
@EnableEurekaServer
```

#### 集群搭建

##### 配置

```yml
server:
	port: 7001

eureka:
	instance:
		hostname: localhost  # eureka 服务端实例名称
	client:
		register-with-erueka: false  # 表示是否向eureka注册自己 集群配置使用
		fetch-registtry: false # 如果为false 则表示自己为注册中心
		service-url: 
			defaultZone: http://${eureka.instance.hostname}:7002/eureka/, http://${eureka.instance.hostname}:7003/eureka/ # 绑定其他集群注册中心的地址
	
```



## API

存放接口和实体类

### pom

```xml
<parent>
    <groupId>com.itianeru</groupId>
    <artifactId>springcloud</artifactId>
    <version>1.0</version>
</parent>
<modelVersion>4.0.0</modelVersion>
<artifactId>springcloud-api</artifactId>

<packaging>jar</packaging>

<dependencies>
    <!-- lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <!--fegin-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-feign</artifactId>
    </dependency>
</dependencies>

```

### api

```java
package com.itianeru.springcloud.api;
    
public interface 
```

### 实体

```java
package com.itianeru.springcloud.pojo;
@Data
@NoArgsConstructor
@Accessors(chain = true)  // 支持链式写法
public class User implements Serializable{
    private static final long serialVersionUID = // 值使用idea生成;
    
    private int id;
    private String username;
    private String pwd;
}
```



## 生产者

### 添加依赖

```xml
<parent>
    <groupId>com.itianeru</groupId>
    <artifactId>springcloud</artifactId>
    <version>1.0</version>
</parent>
<modelVersion>4.0.0</modelVersion>
<artifactId>springcloud-provider</artifactId>

<packaging>jar</packaging>

<dependencies>
    <!-- 实体类 -->
    <dependency>
        <groupId>com.itianeru</groupId>
        <artifactId>springcloud-api</artifactId>
    </dependency>
    <!-- junit -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
    <!-- 数据库 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <!-- druid -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
    </dependency>
    <!-- springcloud -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
    <!-- eureka服务监控详情页 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!-- springboot-web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- springboot-jetty-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
    <!-- mybatis -->
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <!-- log4j -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
    </dependency>
    <!-- 热部署工具 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

### 添加配置

```yml
server:
	port: 8001
spring:
	application:
    	name:  # 服务名
  	datasource:
  		type: com.alibaba.druid.pool.DruidDataSource  # 数据库连接池
  		driver-class-name: com.mysql.jdbc.Driver
  		url: jdbc:mysql://ip:3306/databaseName?useSSL=true&useUnicode=true&characterEncodingUTF-8
  		username: root
  		password: 123456

# eureka配置
eureka:
	client:
		service-url: 
			defaultZone: http://localhost:7001/eureka/   # 注册中心地址
	instance:
		instance-id: # 服务名 会显示在eureka的监控页上
		prefer-ip-address: true # 鼠标放到服务名上, 左下角能显示出该服务的真实ip
# 配置eureka 服务监控详情一json格式返回, 需要导入jar包		
info:
	app.name: itianeru-userservice  # 服务名
	company.name: alibaba   # 公司名

mybatis:
	type-aliases-packate: # pojo包路径
	config-location: classpath:mybatis-config.xml  # 详情看mybatis笔记(可选)
	mapper-location: classpath:mapper/*.xml
```

### 添加注解

```java
// 在启动类添加注解
@EnableEurekaClient
```

### 使用

```java
@RestController
public class UserController{
    @Autowired
    private UserSerivce userService;
    
    // 获取微服务的信息
    @Autowired
    private DiscoveryClient client;
    
    @PostMapping("/user")
    public boolean addUser(User user){
        return userService.add(user);
    }
    
    // 使用需要在启动类加注解  @EnableDiscoveryClient
    @GetMapping("/user/discovery")
    public Object discovery(){
        // 获取微服务列表清单
        List<String> services = client.getServices();
        // 得到具体的微服务
        List<ServiceInstance> instances = client.getInstance("微服务id, 在监控页面上看");
        return this.client;
    }
}
```

## 消费者

### 添加依赖

```xml
<parent>
    <groupId>com.itianeru</groupId>
    <artifactId>springcloud</artifactId>
    <version>1.0</version>
</parent>
<modelVersion>4.0.0</modelVersion>
<artifactId>springcloud-provider</artifactId>

<packaging>jar</packaging>

<dependencies>
    <!-- 实体类 -->
    <dependency>
        <groupId>com.itianeru</groupId>
        <artifactId>springcloud-api</artifactId>
    </dependency>
    <!-- springcloud -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
    <!-- ribbon -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-ribbon</artifactId>
    </dependency>
    <!-- springboot-web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 热部署工具 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

### 添加配置

```yml
server:
	port: 8011

# eureka配置
eureka:
	client:
		register-with-erueka: false # 不向注册中心注册
		service-url: 
			defaultZone: http://localhost:7001/eureka/,http://localhost:7002/eureka/,http://localhost:7003/eureka/  # 注册中心地址
  	
```

### 配置类

```java
@Configuration
public class ConfigBean{
    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
    
    // 修改负载均衡策略
    @Bean
    public IRule ribbonRule() {
        return new RandomRule();//这里配置策略，和配置文件对应
    }
}
```

### 添加注解

```java
// 在启动类添加注解
@EnableEurekaClient
```

### 调用服务

```java
@Autowired
private RestTemplate restTemplate;

restTemplate.getForObject("请求的接口路径", String.class); // 第二个参数为返回值类型
```

### 负责均衡

#### 方式一: LoadBalancerClient

默认使用轮询的方式(挨个调一遍) 

##### 使用

```java
// 注入LoadBalancerClient对象
@Autowired
private LoadBalancerClient loadBalancerClient;
```

```java
// 负载均衡方式选择一个服务, choose()的入参是选择的服务名
ServiceInstance serviceInstance = loadBalancerClient.choose("mango-producer");
// 获取服务地址
serviceInstance.getUri();
// 获取服务名
serviceInstance.getServi ceId();
// 调用服务时, 可动态获取服务地址与接口拼接, 实现负载均衡
new RestTemplate().getForObject(serviceInstance.getUri().toString() + "/hello", String.class);
```

#### 方式二: 负载均衡器(Ribbon)

默认使用轮询

##### 添加配置类

```java
@Configuration
public class ConfigBean{
    @Bean
    @LoadBalanced // 该注解实现负载均衡
    public RestTemplate restTemplate(){
        return  new RestTemplate()
    }
}
```

##### 使用

```java
// 注入 restTemplate
@Autowired
private RestTemplate restTemplate;
```

```java
// restTemplate会自动实现负载均衡
// 接口路径为: 服务名 + 接口
restTemplate.getForObject("http://服务名/接口名", String.class);
```

##### 负载均衡策略

###### 配置文件

```yml
# 在消费者的配置文件中添加
# 生产者的服务名
生产者服务名:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 配置负载均衡粗略. 需要与java配置中对应
```

| 策略类                    | 命名             | 说明                                                         |
| ------------------------- | ---------------- | :----------------------------------------------------------- |
| RandomRule                | 随机策略         | 随机选择 Server                                              |
| RoundRobinRule            | 轮训策略         | 按顺序循环选择 Server                                        |
| RetryRule                 | 重试策略         | 在一个配置时问段内当选择 Server 不成功，则一直尝试选择一个可用的 Server |
| BestAvailableRule         | 最低并发策略     | 逐个考察 Server，如果 Server 断路器打开，则忽略，再选择其中并发连接最低的 Server |
| AvailabilityFilteringRule | 可用过滤策略     | 过滤掉一直连接失败并被标记为 `circuit tripped` 的 Server，过滤掉那些高并发连                     接的 Server（active connections 超过配置的网值） |
| ResponseTime WeightedRule | 响应时间加权策略 | 根据 Server 的响应时间分配权重。响应时间越长，权重越低，被选择到的概率就越低；响应时间越短，权重越高，被选择到的概率就越高。这个策略很贴切，综合了各种因素，如：网络、磁盘、IO等，这些因素直接影响着响应时间 |
| ZoneAvoidanceRule         | 区域权衡策略     | 综合判断 Server 所在区域的性能和 Server 的可用性轮询选择 Server，并且判定一个 AWS Zone 的运行性能是否可用，剔除不可用的 Zone 中的所有 Server |

# 服务消费(Fegin)



### 添加依赖

```xml
<!--fegin   还有一种 openfegin可学习-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

### 添加注解

```java
// 在启动类添加注解, 扫描接口
@EnableFeignClients(basePackages = {"com.itianeru.xxx"})
```

### 使用

```java
// 写在api包中
// 创建调用服务的接口
// @FeignClient指定服务名
@FeignClient(name = "服务名")
public interface ProducerService {
	// 指定接口名
    @RequestMapping("/hello")
    public String hello();
}
```

```java
// 注入创建的接口
@Autowired
private ProducerService producerService;

@RequestMapping("/fegin/call")
public String call(){
    // 直接调用接口的方法, 即可调用远程服务的接 口
    return producerService.hello();
}

```

# 熔断器(Hystrix)



熔断器会在多节点服务中, 某个节点挂掉, 在调用这个挂掉的节点时, 熔断器会调用指定的回调类去处理, 避免多次调用,造成服务器阻塞

### 添加依赖

```xml
<!-- Fegin的依赖中包含了Hystrix, 如果不使用Fegin, 需要单独引入Hystrix-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```

### 添加配置

```yml
# 开启熔断器
feign:
  hystrix:
    enabled: true
```

或者使用注解

```java
@EnableCircuitBreaker
```

### 类实现熔断

#### 创建回调类

```java
// 实现被调用的服务接口
@Component
public class MangoProducerHystrix implements MangoProducerService {
    @Override
    @RequestMapping("/hello")
    public String hello() {
        return "sorry hello service call faild";
    }
}
```

#### 配置回调类

```java
// 在fallback中指定回调类
@FeignClient(name = "mango-producer", fallback = MangoProducerHystrix.class)
public interface MangoProducerService {

    @RequestMapping("/hello")
    public String hello();
}
```

### 方法实现熔断

```java
public class xxxController{
    @HystrixCommand(fallbackMethod = "hget")
	@GetMapping("/user")
    public User get(Long id){
        return xxx;
    }
    
    // 熔断时触发的方法
    public User hget(Long id){
        return "备选";
    }
}
```



# 仪表盘(Hystrix-dashboard)

仪表盘可对熔断器监控, 监控每次请求, 需要建独立的工程

### 添加依赖

```xml
<dependencies>
    <!-- consul -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>
    <!-- 监控客户端 -->
    <dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-starter-client</artifactId>
        <version>2.0.4</version>
    </dependency>
    <!-- actuator-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
        <version>2.0.4.RELEASE</version>
    </dependency>
    <!-- 仪表盘-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        <version>2.0.4.RELEASE</version>    
    </dependency>
</dependencies>

<!--srping cloud-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Finchley.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 添加注解

```java
// 在启动类添加注解
@EnableHystrixDashboard  // 启动仪表盘
@EnableDiscoveryClient
```

### 添加配置

```yml
server:
  port: 8501
spring:
  application:
    name: mango-hystrix
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
```

### 配置监控路径

在消费者consumer中配置

#### 添加依赖

```xml
 <!-- actuator-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>2.0.4.RELEASE</version>
</dependency>
<!-- 仪表盘-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
    <version>2.0.4.RELEASE</version>    
</dependency>
```

#### 添加java配置

```java
// 在启动类添加
@Bean
public ServletRegistrationBean  getServlet(){
    HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
    ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
    registrationBean.setLoadOnStartup(1);
    registrationBean.addUrlMappings("/hystrix.stream");
    registrationBean.setName("HystrixMetricsStreamServlet");
    return registrationBean;
}
```

### 使用

```
访问 http://localhost:8501/hystrix/
在输入框中填入, 要监控的接口, 在调用接口时, 即可监控
```

# 增强仪表盘(Turbine)

Hystrix-dashboard只能监控单个应用的信息, Turbine可汇总多个系统的信息到Hystrix-dashboard, 在仪表盘的项目添加Turbine

### 添加依赖

```xml
<!-- turbine -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-turbine</artifactId>
    <!-- 排除注册中心eureka，防止预consul冲突 -->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### 添加注解

```java
// 在启动类添加
@EnableTurbine
```

### 添加配置

```yml
turbine:
  instanceUrlSuffix: hystrix.stream  # 指定收集路径
  app-config: mango-consumer # 指定要监控的服务名， 多个服务，用 ‘,’ 分割
  cluster-name-expression: "'default'"  # 指定集群名称， default为默认集群
  combine-host-port: true  # 该配置设置服务的区分方式， 为false时，以port进行区分， 为true时， 以host+port进行区分
```

### 使用

```
访问 http://localhost:8501/hystrix/
在输入框中填入 http://localhost:8501/hystrix.stream 即可监控
```

# 服务网关(Zuul)

### 添加依赖

```xml
<dependencies>
    <!-- zuul -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        <version>2.0.4.RELEASE</version>
    </dependency>
    <!--consul-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>
</dependencies>
<!--srping cloud-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Finchley.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 添加注解

```java
// 在启动类添加注解, 启动网关服务
@EnableZuulProxy
```

### 添加配置

```yml
server:
  port: 8010

spring:
  application:
    name: mango-zuul
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
zuul:
  # zuul网关默认会代理所有注册到注册中心的服务, 可不用配置routes
  routes: 
    ribbon:
      path: /ribbon/**
      serviceId: mango-consumer  # 转发到消费者
    feign:
      path: /feign/**
      serviceId: mango-consumer
  prefix: /v1  # 接口前缀, 一般用来显示版本号
```

### 使用

```
# 8010为zuul的端口号, v1为前缀, ribbon和fegin是routes下面配置的路由
http://ip:8010/v1/ribbon/接口名称
http://ip:8010/v1/fegin/接口名称

# 在没有配置routes的情况下, 可通过服务名进行访问
http://ip:8010/v1/mango-consumer/ribbon/接口名称
```

### 熔断器

编写熔断器回调类, 如果zuul网关的路由使用默认配置, 有可能会出现在服务挂掉, 该熔断器失效的情况, 因为zuul网关的默认配置是代理注册中心上的服务, 服务挂掉有可能导致zuul网关无法找到代理的服务, 还没有经过熔断器, 所以熔断器无效.此时可通过编写网关的路由, 解决该问题

```java
@Component
public class MyFallbackProvider implements FallbackProvider {
    @Override
    public String getRoute() {
        // 返回指定的服务名， 指定该熔断器用于那个服务， 如果所有服务都使用， 可用返回 '*'
        return "mango-consumer";
    }
    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        System.out.println("route:" + route);
        System.out.println("exception" + cause.getMessage());
        return new ClientHttpResponse() {
            @Override
            public HttpStatus getStatusCode() throws IOException {
                return HttpStatus.OK;
            }

            @Override
            public int getRawStatusCode() throws IOException {
                return 200;
            }

            @Override
            public String getStatusText() throws IOException {
                return "OK";
            }

            @Override
            public void close() {

            }

            @Override
            public InputStream getBody() throws IOException {
                return new ByteArrayInputStream("服务异常， 请重试".getBytes());
            }

            @Override
            public HttpHeaders getHeaders() {
                HttpHeaders headers = new HttpHeaders();
                headers.setContentType(MediaType.APPLICATION_JSON);
                return headers;
            }
        };
    }
}
```

### 过滤器

可对请求进行过滤, 无效的请求会被过滤掉

```java
@Component
public class MyFilter extends ZuulFilter {

    @Override
    public String filterType() {
        // 过滤类型
        // pre：可以在请求被路由之前调用
        // route：在路由请求时候被调用
        // post：在route和error过滤器之后被调用
        // error：处理请求时发生错误时被调用
        return "pre";
    }

    @Override
    public int filterOrder() {
        // 过滤顺序
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        // 是否启动过滤
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        // 过滤操作
        RequestContext ctx = RequestContext.getCurrentContext();
        HttpServletRequest request = ctx.getRequest();
        String token = request.getParameter("token");
        System.out.println("token=" + token);
        if (token == null || token.equals("")){
            // 设置是否被过滤, false为被过滤掉请求
            ctx.setSendZuulResponse(false);
            ctx.setResponseStatusCode(401);
            ctx.setResponseBody("token为空");
            return null;
        }else {
            ctx.setSendZuulResponse(true);
            return null;
        }
    }
}
```

# 链路追踪(ZipKin,Sleuth)

### ZipKin

主要用于采集服务的时间, 解决微服务架构延迟问题

### Sleuth

可通过Sleuth了解到一个请求经过哪些服务,了解各个服务的调用关系

# 配置中心

## 服务端

### 添加依赖

新建项目, 添加依赖

```xml
<!-- spring config -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
    <version>2.0.4.RELEASE</version>
</dependency>
<!-- consul -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>

<!--srping cloud-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Finchley.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 添加注解

```java
// 在启动类添加注解
@EnableConfigServer
@EnableDiscoveryClient
```

### 添加配置

```yml
server:
  port: 8020

spring:
  application:
    name: mango-config
#  profiles:            # 该配置可读取本地的配置文件, 启动该配置后, 无需配置下面的config
#    active: native
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
    # 云端配置
    config:
      label: master # 分支
      server:
        git:
          uri: https://gitee.com/ITianerU/configuration_center.git # 配置git仓库， 这里用码云
          search-paths: config-repo  # 配置文件所在的文件夹路径
          username: username
          password: password
```

### 使用

#### 本地

```properties
# 在resources添加配置文件
# 如 ~ consumer-dev.properties
hello=hello,dev configurations local.
```

```
启动项目
访问 http://192.168.42.95:8020/consumer/dev 即可读取到配置
访问 http://192.168.42.95:8020/consumer-dev.properties 可读取文件内容
```

#### 云端

```properties
# 在代码仓库中创建配置文件, 与本地用法相同
```

## 客户端

### 添加依赖

在consumer中添加依赖

```xml
<!-- spring-cloud-config -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
    <version>2.0.4.RELEASE</version>
</dependency>
```

### 添加配置

新建bootstrap.yml, 将application.yml中的部分配置移过来, config是新加的配置

```yml
spring:
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
    config:
      discovery:
        enabled: true  # 开启服务发现
        service-id: mango-config  # 配置服务名称
      # 访问 http://localhost:8020/name/profile
      name: consumer  # 对应name部分
      profile: dev  # 对应profile部分
      label: master  # 对应git分支
```

### 使用

```java
// 读取配置文件, 将配置文件中key为hello的值注入
@Value("{hello}")
private String hello;
```

### Refresh机制

springboot默认只在启动时, 读取一次配置文件, 所以在云端修改配置文件后, 客户端不会读取修改后的内容, 所以加入Refresh机制

#### 添加依赖

```xml
<!-- actuator-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>2.0.4.RELEASE</version>
</dependency>
```

#### 添加注解

```java
// 在controller的类上添加
@RefreshScope
```

#### 添加配置

在application.yml中添加

```yml
# 暴露端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

### 使用

在修改云端的配置文件后, 以post形式, 调用http://localhost:port/actuator/refresh

就会刷新服务读取到的配置文件

## 消息总线(Spring Cloud Bus)

略