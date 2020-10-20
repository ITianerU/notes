# 依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.8.RELEASE</version>
</dependency>
<!-- <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2</version>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency> -->
```

# 配置

## web.xml

```xml
<!-- DispatcherServlet -->
<servlet>
	<servlet-name>springmvbc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
    	<param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<!-- / 匹配所有请求 不包括 .jsp -->
<!-- /* 匹配所有请求 包括 .jsp -->
<servlet-mapping>
	<servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

## spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" 
       xmlns:mvc="http://www.springframework.org/schema/mvc" 
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
                        http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
                        http://www.springframework.org/schema/mvc
    http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	
    
    <!-- 处理器映射器(非注解开发需要配置) -->
    <!-- <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping" />-->
    <!-- 处理器适配器(非注解开发需要配置) -->
    <!-- <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter" />-->
    
    <!-- 注解开发 -->
    <!-- 自动包扫描 -->
    <context:component-scan base-package="com.itianeru.controller"></context:component-scan>
    <!-- 让spring mvc不处理静态资源 -->
    <!-- <mvc:default-servlet-handler /> -->
    <!-- 自动注册处理器映射器DafultAnnotationHadlerMapping 和处理器适配器 AnnotationMethodHandlerAdapter -->
    <!-- 不注册不能使用@RequestMapping-->
    <mvc:annotation-driver />
    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" 			
          id="InternalResourceViewResolver">
    	<property name="prefix" value="/WEB-INF/jsp/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
</beans
```

# 原理

## 工作流程

- 浏览器发出请求到**前端控制器(DispatcherServlet)**
- **前端控制器**接收到请求后, 调用**处理器映射器(HandlerMapping)**,通过请求url去查找对应的**处理器(HandlerExecution)**
- **处理器**根据url去查找**控制器(Controller)**
- **处理器**将解析后的信息传递给**前端控制器**
- **前端控制器**通过解析后的信息, 调用**处理器适配器(HandlerAdapter)**
- **处理器适配器**去执行对应的**控制器**
- **控制器**返回数据, 以及对应的页面信息给**前端控制器**
- **前端控制器**调用**视图解析器(ViewResolver)**解析出视图,
- **前端控制器**将解析出的视图返回给浏览器 

# 开发

## RestFul风格

@PathVariable注解

```java
@GetMapping("/test/{a}/{b}")
// 或者@RequestMapping(value="/test/{a}/{b}", method=RequestMethod.GET)
public String test(@PathVariable int a,@PathVariable int b){
    // 巴拉巴拉
}
```

调用

http://localhost:8080/test/1/2

## 重定向与请求转发

```java
@GetMapping("/test")
public String test(){
    // 转发
    return "test";
    // 重定向
    return "redirect:test";
    return "redirect:/index.jsp";
}
```

## 乱码问题

web.xml配置过滤器

```xml
<filter>
	<filtername>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
    	<param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
	<filtername>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

tomcat设置编码

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1" connectionTimeout="20000" 
           redirectPort="8443"/>
```

