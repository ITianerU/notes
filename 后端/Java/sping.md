# 地址

## 官网

https://spring.io/projects/spring-framework#overview

## 源码地址

https://repo.spring.io/release/org/springframework/spring/

## github地址

https://github.com/spring-projects/spring-framework

# 组成

spring有七大模块,

**Spring Core**

核心, 其他模块都依赖与此模块

**Spring AOP**

面向切面

**Spirng ORM**

对象关系映射

**Spring DAO**

对于持久层的支持

**Spring Web**

**Spring Context**

**Spring Web MVC**

# 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<!-- 该包包含spring其他核心包 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.8.RELEASE</version>
</dependency>
```

# 配置文件

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:context="http://www.springframework.org/schema/context" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:p="http://www.springframework.org/schema/p" xmlns:util="http://www.springframework.org/schema/util" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:cache="http://www.springframework.org/schema/cache"
    xsi:schemaLocation="
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx.xsd
    http://www.springframework.org/schema/jdbc
    http://www.springframework.org/schema/jdbc/spring-jdbc-3.1.xsd
    http://www.springframework.org/schema/cache
    http://www.springframework.org/schema/cache/spring-cache-3.1.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/util
    http://www.springframework.org/schema/util/spring-util.xsd">
 
    <!-- 自动扫描web包 ,将带有注解的类 纳入spring容器管理 -->
    <context:component-scan base-package="com.eduoinfo.finances.bank.web"></context:component-scan>
 
    <!-- 引入jdbc配置文件 -->
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath*:jdbc.properties</value>
            </list>
        </property>
    </bean>
 
    <!-- dataSource 配置 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <!-- 基本属性 url、user、password -->
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
 
        <!-- 配置初始化大小、最小、最大 -->
        <property name="initialSize" value="1" />
        <property name="minIdle" value="1" />
        <property name="maxActive" value="20" />
 
        <!-- 配置获取连接等待超时的时间 -->
        <property name="maxWait" value="60000" />
 
        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
        <property name="timeBetweenEvictionRunsMillis" value="60000" />
 
        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
        <property name="minEvictableIdleTimeMillis" value="300000" />
 
        <property name="validationQuery" value="SELECT 'x'" />
        <property name="testWhileIdle" value="true" />
        <property name="testOnBorrow" value="false" />
        <property name="testOnReturn" value="false" />
 
        <!-- 打开PSCache，并且指定每个连接上PSCache的大小 -->
        <property name="poolPreparedStatements" value="false" />
        <property name="maxPoolPreparedStatementPerConnectionSize" value="20" />
 
        <!-- 配置监控统计拦截的filters -->
        <property name="filters" value="stat" />
    </bean>
 
    <!-- mybatis文件配置，扫描所有mapper文件 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean" p:dataSource-ref="dataSource" p:configLocation="classpath:mybatis-config.xml" p:mapperLocations="classpath:com/eduoinfo/finances/bank/web/dao/*.xml" />
 
    <!-- spring与mybatis整合配置，扫描所有dao -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" p:basePackage="com.eduoinfo.finances.bank.web.dao" p:sqlSessionFactoryBeanName="sqlSessionFactory" />
 
    <!-- 对dataSource 数据源进行事务管理 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" p:dataSource-ref="dataSource" />
 
    <!-- 配置使Spring采用CGLIB代理 -->
    <aop:aspectj-autoproxy proxy-target-class="true" />
 
    <!-- 启用对事务注解的支持 -->
    <tx:annotation-driven transaction-manager="transactionManager" />
 
    <!-- Cache配置 -->
    <cache:annotation-driven cache-manager="cacheManager" />
    <bean id="ehCacheManagerFactory" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean" p:configLocation="classpath:ehcache.xml" />
    <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager" p:cacheManager-ref="ehCacheManagerFactory" />
 
</beans>
```

## 加载配置文件

```java
// 构造可传递多个参数, 读取多个配置文件
// 获取spring 上下文对象
ApplicationContext context = new ClassPathXmlApplication("applicationContext.xml");
```

# 核心

## IOC

降低耦合度, 将创建对象交给spring, 减少硬编码.

在项目启动时, 就会创建实例, 默认是单例模式.

**推荐**  xml管理bean, 注解实现注入.

### xml方式

#### 实现

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 		 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd">
    
	<!-- 使用无参构造创建实例 -->
    <!-- name为别名, 可同时命名多个别名, 可通过,(逗号) ;(分号) 空格分隔 -->
    <bean id="user" class="com.itianeru.pojo.User" name="newUser,newUser2">
        <!-- 初始化属性, 会通过set方法, 注入数据 -->
        <property name="username" valu="老王" />
        <!-- 引用别的对象 -->
        <property name="job" ref="job" />
    </bean>
    
    <!-- 使用有参构造创建实例 -->
    <bean id="user" class="com.itianeru.pojo.User">
        <!-- 通过下标赋值 -->
        <constructor-arg index="0" valu="老王" />
        <!-- 通过类型赋值(不建议使用, 可能有重复类型) -->
        <constructor-arg type="java.lang.String" valu="老王" />
        <!-- 通过参数名赋值 -->
        <constructor-arg  name="username" value="老王" />
        <constructor-arg  name="job" ref="job" />
    </bean>
    
    <bean id="job" class="com.itianeru.pojo.Job">
    	<!-- 初始化属性 -->
        <property name="jobName" valu="Java开发" />
    </bean>
    
    <!-- 别名(不常用, 通常使用name别名), 不影响原来id的名 -->
    <alias name="user" alias="别名">
        
    <!-- 可导入多个配置文件, 合并为一个 -->    
    <import resource="beans.xml">
</beans>
```

#### 获取对象

```java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
// 从spring容器中, 通过id获取类对象
User user = (User)context.getBean("user");
// 也可以通过别名, 获取对象实例
User user = (User)context.getBean("别名");
```

#### 依赖注入

- 依赖: bean对象的创建依赖于容器
- 注入: bean对象的所有属性, 通过容器来注入

##### 构造器注入

上面已写

##### Set方式注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 		 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="user" class="com.itianeru.pojo.User">
        <!-- 普通数据注入 -->
        <property name="username" valu="老王" />
        <!-- 复杂对象注入 -->
        <property name="job" ref="job" />
        <!-- 数组注入 -->
        <property name="girlfirends">
            <array>
            	<value>花花</value>
            	<value>草草</value>
            </array>
        </property>
        <!-- list注入 -->
        <property name="girlfirends">
            <list>
            	<value>花花</value>
            	<value>草草</value>
            </list>
        </property>
         <!-- map注入 -->
        <property name="girlfirends">
            <map>
            	<entry key="first" value="花花" />
            	<entry key="second" value="草草" />
            </map>
        </property>
        <!-- set注入 -->
        <property name="girlfirends">
            <set>
            	<value>花花</value>
            	<value>草草</value>
            </set>
        </property>
        <!-- null注入 -->
        <property name="son">
            <null />
        </property>
        
        <!-- props注入 -->
        <property name="girlfirends">
            <props>
            	<prop key="first">花花</prop>
            	<prop key="second">草草</prop>
            </props>
        </property>
    </bean>
</beans>
```

##### 扩展方式注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 引入p和c命名空间的约束-->
<beans xmlns="http://www.springframework.org/schema/beans" 		 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:p="http://www.springframework.org/schema/p" 
       xmlns:c="http://www.springframework.org/schema/c" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- p命名空间注入, 属性注入 -->
    <bean id="user" class="com.itianeru.pojo.User" p:username="老王" p:age="28" p:spouse-ref="job" />
    <!-- c命名空间注入, 有参构造注入 -->
    <bean id="user" class="com.itianeru.pojo.User" c:username="老王" c:age="28" p:name-ref="job" />
    <bean id="job" class="com.itianeru.pojo.Job">略</bean>
</beans>
```

#### 作用域

scope 可选值

- **singleton**  单例(默认)

- **prototype**   原型, 非单例, 每次获取都创建新的实例

  下面的仅在web中使用

- **request**

- **session**

- **application**

- **websokcet**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 		 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- scope 作用域 -->
    <bean id="job" class="com.itianeru.pojo.Job" scope="prototype">略</bean>
</beans>
```

#### 自动装配

autowired

- **byName**  通过属性名与bean的id名做匹配, 首字母小写
- **byType**  通过类型做匹配, 需保证要装备的类型全局唯一

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 		 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- autowired 自动装配 -->
    <bean id="user" class="com.itianeru.pojo.User" autowired="byName">略</bean>
    <bean id="job" class="com.itianeru.pojo.Job">略</bean>
</beans>
```

### 注解方式

在spring4之后, 要使用注解开发, 必须要保证aop包的导入.

使用注解, 需要导入context约束, 增加注解的支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 		 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!-- 指定扫描的包, 这个包下的注解会生效 -->
    <context:component-scan base-package="com.itianeru.*" />
    <!-- 要使用@ Resource 、@ PostConstruct、@ PreDestroy、@PersistenceContext、@Autowired、@Required 等注解, 需要声明 -->
    <context:annotation-config />
</beans>
```

```java
@Component
@Scope("singleton")
public class User(){
    @Value("老王")
    private String username;
}
```

### java方式(推荐)

```java
@Configuration
@ComponentScan("com.itianeru.*")
// 引入别的配置类
@Import(Config2.class)
public class Config{
    
    @Bean
    public UserService getUserService(){
        return new UserService();
    }
}
```

```java
@Component
public class UserService{}
```

#### 获取对象

```java
ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
// 从spring容器中, 通过配置类的方法名获取对象
UserService user = (User)context.getBean("getUserService");
```

## AOP

### 代理模式

#### 静态代理

```java
// 代理类
public static Proxy{
    // 被代理的对象
    private UserService userService;
    public Proxy(UserService userService){
        this.userService = userService;
    }
    
    public void doSomeThing(){
        // 代理类操作一些公共业务逻辑
        System.out.println("做了一些事");
        // 执行被代理对象的方法.
        this.userService.selectOne();
        System.out.println("做了一些事");
    }
}
```



#### 动态代理

相对于静态代理, 一个动态代理类可代理多个实现了同一个接口的类

##### 基于接口

- jdk动态代理

  接口

  ```java
  public interface Rent{
      public void rend();
  }
  ```

  实现类

  ```java
  public class Host implements Rent{
      public void rend(){ doSomething.... }
  }
  ```

  代理类

  ```java
  public class ProxyInvocationHandler implements invocationHadler {
      // 被代理的类
      private Object target;
      
      public void setTarget(Object target){
          this.target = target;
      }
   
      // 获取被代理的类的接口, 生成代理对象
      public Object getProxy(){
          return Proxy.newProxyInstance(this.getClass().getClassLoader(), 
                                        target.getClass().getInterface(), this);
      }
      
      // 处理代理实例
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
          seeHouse();
          Object result = method.invoke(target, args);
          seeHouse();
          return  result;
      }
      
      public void seeHouse(){
          // 看房子
      }
  }
  ```

  调用

  ```java
  Host host = new Host();
  // 代理角色
  ProxyInvocationHandler pih = new ProxyInvocationHandler();
  // 设置要代理的对象
  pih.setRent(host);
  // 动态生成代理类
  Rent proxy = (Rent)pih.getProxy();
  proxy.rend();
  ```

  

##### 基于类

- cjlib

##### java字节码实现

- ##### Javassist

# 常用注解

## 实例

**@Component, @Controller, @Service, @Repository**  功能完全相同, 只为了做控制层, 业务层, 持久层区分.

**@RestController**  返回json数据.

## 自动装配

**@Autowired**: 自动装配, 通过类型和名字, 如果通过类型装配找到多个实例, 则需要**@Qualifier(value="xxx")**

- **required=false**  非必须注入实例

```java
@Service("userService1")
public class UserServiceImpl1(){}

@Service("userService2")
public class UserServiceImpl2(){}

@Autowired
// 有两个UserService类型的实例, 指定要注入哪一个
@Qualifier("userService1")
UserService userService;
```

**@Resource**: 自动装配通过名称, 类型, 默认名称

- **name="xxx"**   通过名称装配
- **type="xxx"**   通过类型装配

## 其他

**@Value**   赋值, 用在属性或者set方法上

**@Scope**   设置作用域, 单例, 原型等

**@Configuration**   声明的类的配置类

**@Import**  引入别的配置类

**@ComponentScan**   包扫描

**@Bean**   创建bean