# 配置

```yml
debug: true  # 开启配置类日志输出
server:
  port: 8001  # 端口
  servlet.cpmtext=path: /itianeru  # 项目访问路径 http://localhost:8080/itianeru
spring:
  application:
    name: xxx # 项目名
```

## yaml语法(推荐使用, 支持的语法多)

```yml
# 行内写法
persion: {name: laowang, age: 13}
# 数组
students:
	- laowang
	- xiaohong
# 数组行内写法
students: [stu1, stu2]
```

### yaml注入数据

实体类

```java
@Component
/* 
     通过该注解, 指定yaml中配置的数据前缀后, 自动注入到实体类的属性中
     使用该注解, idea会报红, 不影响开发, 可以添加如下依赖解决
     <dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-configuration-processor</artifactId>
		<optional>true</optronal>
     </dependency>
     实体类需要有参构造
*/ 
@ConfigurationProperties(prefix = "person")
public class Person{
    private String name;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
}
```

yaml

```yaml
person:
	name: 老王${random.int}    #   随机数 ${random.uuid} 
	birth: 1998/12/12
	maps: {k1: v1, k2: v2}
	lists:
		- img
		- music
	dog:
		name: ${person.name:花花}    # 三元表达式, 如果存在persion.name则使用, 不存在则使用冒号后面的值
		age: 3
```

## properties语法

### properties注入数据

实体类

```java
@Component
/* 
     通过该注解, 可以指定自定义的properties文件中的数据, 手动通过@Balue注入到实体类的属性中
     实体类需要有参构造
*/ 
@PropertySource(value = "classpath:person.properties")
public class Person{
    @Value("${name}")
    private String name;
}
```

properties

```properties
name=老王
```

## 配置文件位置

按优先级从高到低排序

- 根目录下的config目录下

  ```yml
  demo:  # 项目名
  	config:  # 放在这里
  		application.yml  # 配置文件
  	src:   # 代码
  	pom.xml
  ```

- 根目录下

  ```yml
  demo:  # 项目名
  	application.yml  # 配置文件
  	src:   # 代码
  	pom.xml
  ```

- 代码目录下/src/main/resources/config

  ```yml
  demo:  # 项目名
  	src:   # 代码
  		main:
  			java:
  			resources:
  				config:
  					application.yml  # 配置文件
  	pom.xml
  ```

- 代码目录下/src/main/resources/

  ```yml
  demo:  # 项目名
  	src:   # 代码
  		main:
  			java:
  			resources:
  				application.yml  # 配置文件
  	pom.xml
  ```

## 多环境配置文件切换

### 写多个配置文件

- application-test.yml   测试环境
- application-dev.yml   研发环境
- application-prod.yml   生产环境

切换配置文件

application.yml

```yml
spring:
	profiles:
		active: test
```

### 一个配置文件

通过三个横杠 --- 来划分不同环境的配置

application.yml

```yml
spring:
	profiles:
		active: test   # 指定使用哪个配置
---
server:
	port: 8081
spring:            
	profiles: test      # 设置配置名称
---
server:
	port: 8082
spring:            
	profiles: dev      # 设置配置名称

```

## spingmvc配置

### 基本配置

```properties
# 配置日期格式
spring.mvc.date-format="yyyy-MM-dd"
```

### Java配置

```java
package com.itianeru.config

// 如果想定制化一些功能, 只需要写这个组件, 然后@Bean交给springboot, spirngboot会自动装配
@Configuration
public class MyMvcConfig implements WebMvcConfigurer{
    
    @Bean
    public ViewResolver myViewResolver(){
        return new MyViewResolver();
    }
    
    // 手动添加页面请求跳转映射
   	// 可设置不同的请求路径, 跳转相同的页面
    // 等价于@RequestMapping({"/test", "/test2"})
    @Override
    public void addViewControllers(ViewCOntrollerRegistry registry){
		registry.addViewController("/test").setViewName("test");
         registry.addViewController("/test2").setViewName("test");
    }
}
```

```java
// 自定义视图解析器
public class MyViewResolver implements ViewResolver{
    @Override
    public View resolveViewName(String viewName, Locale locale) throws Exception {
        return null;
    }
}
```



# 原理

## 自动配置

### 注解

- @SpringBootApplication   声明这个类是springboot应用的启动类
  - @SpringBootConfiguration   springboot配置
    - @Configuration    声明spring配置类
      - @Component    说明这是个组件
  - @EnableAutoConfiguration    自动配置
    - @AutoConfigurationPackage    自动配置包
      - @Import(AutoConfigurationPackages.Register.class)   自动配置包注册
    - @import(AutoConfigurationImportSelector.class)   导入自动选择类
      - getAutoConfigurationEntry()    获得自动配置的实体
        - getCandidateConfigurations()   获取候选的配置
  - @ComponentScan    扫描当前主启动类同级的包

### 原理分析

springboot所有自动配置都是在启动的时候扫描并加载, 但是不一定生效, 会判断条件是否成立, 只有导入的对应的start, 就有了对应的启动器才能生效

- springboot在启动的时候, 从类路径下/META-INF/spring.factories(在spring-boot-autoconfigure包下) 获取指定的类值
- 将这些类导入到容器, 自动配置就会生效

# 使用

## JSR303校验

```java
@Component
// 使用该注解进行数据校验,  
@Validated
public class Person{
    // 在message中指定校验的提示
    // 空值校验
    @Null(message="不能为空")  // 该属性必须为空
    @NotNull  // 非空
   	// 布尔类型校验
    @AssertTrue  // 必须为true
    @AssertFalse // 必须为false
    // 数字校验
    @Min(10)   // 大于等于10
    @DecimalMin(10)  // 大于等于10
    @Max(10)   // 小于等于10
    @DecimalMax(10)  // 小于等于10
    @Size(10, 20)   // 数字范围在10-20之间
    // 日期校验
    @Past   // 必须为过去的日期
    @Future  // 必须为将来的日期
    // 字符串校验
    @Email(message="邮箱格式错误")     // 校验邮箱格式
    @Length(10,20)   // 限制字符串长度
    @NotEmpty // 非空字符串
    @Range  // 长度在指定范围内
    @Pattern()    // 正则
    private String email;
}
```

## 静态资源

## 访问路径配置

### 默认配置

静态资源html, js等可放置在如下目录下, 按访问优先级从高到低排序

访问地址:    http://localhost:8080/静态资源

```yml
demo:  # 项目名
	src:   # 代码
		main:
			resources:
				resources:   # 优先级最高
					静态资源
				static:      # 优先级其次
				public:      # 优先级第三
				templates:   # 只能通过controller跳转访问, 需要导入thymeleaf依赖
```

### 自定义配置(不推荐)

静态资源的访问路径会变成如下的路径

```properties
spring.mvc.static-path-pattern=/itianeru/**
```

### 页面配置

**首页**

在静态资源访问目录添加  index.html

**错误**

在templates下创建error文件夹, springboot会根据错误码, 自动找到对应的页面

- 404   /error/404.html  
- 500   /error/500.html  

**页面图标**

在静态资源访问目录添加favicon.ico的图标 , **部分springboot版本不支持**

关闭默认图标

```properties
spring.mvc.favicon.enabled=false
```

## 模板引擎

#### 依赖

```xml
<dependency>
	<groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
	<groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```

#### 配合

```properties
# 关闭缓存
spring.thymeleaf.cahce=false
```

#### 使用

文件放置在templates下, 需要通过controller跳转

```html
<!-- 导入thymeleaf约束 -->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <!--导入css -->
        <link th:href="@{css路径}" rel="stylesheet"/>
    </head>
    <!-- 输出后端返回的数据 -->
    <span th:text="${msg}"></span>
    <!-- 或者 -->
    <span>[[${msg}]]</span>
    <!-- 带标签语义输出后端返回的数据, 例如  msg="<h1>hello</h1>" -->
    <div th:utext="${msg}"></div>
	<!-- 遍历,  users为集合, user为集合的每一项 -->
    <span th:each="user:${users}" th:text="${user}"></span>
    <!-- if msg不等于空, 才显示-->
    <span th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></span>
    <!-- 图片 -->
    <img th:src="@{图片路径}" />
    <!-- 声明一个组件, 可以在其他地方插入 -->
    <!-- 用于多个页面公用相同的部分 -->
    <div th:fragment="组件名">
        <!-- 接收该组件在使用时传递的参数 -->
    	<span th:calss="${参数名}"></span>
    </div>
    <!-- 插入组件, 需要指明是哪个文件中的哪个组件, 可传递参数 -->
    <div th:insert="~{文件名::组件名(参数名='参数值')}"></div>
    <!-- 替换组件 -->
    <div th:replace="~{文件名::组件名}"></div>
</html>
```

#### 国际化 

在/resources/下创建文件夹 i18n

创建一个配置文件 xxx.properties 和 xxx_zh_CN.properties,  idea会自动合并这两个文件, 并创建一个Resource Bundle文件夹, 右键该文件夹可添加跟多的配置文件

**国家_地区**

- zh_CN  中文
- en_US  英文

点开xxx.properties, 窗口下方有一个Resource Bundle选项, 点开可同时配置多个不同语言配置文件的值

##### 配置

```properties
# xxx为编写的配置文件的名称
spring.messages.basename=i18n.xxx    
```

```java
package com.itianeru.config

// 国际化解析器
@Configuration
public class MyLocaleResolver implements LocaleResolver{
    @Override
    public Locale resovleLocale(HttpServletRequest request) {
        // 获取页面传递的国际化参数
        Styring language = request.getParameter("language");
        // 获取默认的设置
        Locale locale = Locale.getDefault();
        // 如果请求的国际化参数为空, 则使用默认的配置
        if(!StringUtils.isEmpty(language)){
            // 将参数分割为国家和地区
            String[] split = language.split("_");
            locale = new Locale(split[0], split[1]);
        }
        return locale;
    }
}
```

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer{
    
    // 将国际化解析器注册为bean
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
}
```

##### 使用

```html
<!-- 使用#{}会读取国际化配置文件 -->
<span th:text="#{国际化配置文件配置的字段名}"></span>
<!-- 或者 -->
<span>[[#{msg}]]</span>
```

```html
<!-- 切换中英文 -->
<a th:href="@{当前页面路径(language='zh_CN')}">中文</a>
<a th:href="@{当前页面路径(language='en_US')}">English</a>
```

## 拦截器

```java
public class MyInterceptor implements HandlerInterceptor{
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception{
        
    }
}
```

**注册拦截器**

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer{
    
    @Bean
    public void addInterceptors(InterceptorRegistry registry){
        // 配置拦截器
        registry.addInterceptor(new MyInterceptor())
            // 配置哪些请求被拦截
            .addPathPatterns("/**")
            // 排除哪些请求不被拦截,  css, js, img不被排除也会被拦截
            .excludePathPatterns("/index.html", "/index", "/css/*", "/js/*", "/img/*");
    }
}
```

## 数据

### jdbc

#### 依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
	<groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### 配置

```yml
spring:
	datasource:
		username: xxx
		password: xxx
		url: jdbc:mysql://ip:3306/库名?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
		driver-class-name: com.mysql.jdbc.Driver  # 或者 com.mysql.cj.jdbc.Driver (mysql8.0版本以上)
```

#### 使用

```java
public class TestController{
    @Autowired
    private JdbcTemplte jdbcTemplate;
    
    @GetMappint("/user")
    public List<User> test() throws SQLException{
    	return jdbcTemplate.queryForList("select * form user")
    }
}
```

## 异步任务

### 开启异步功能

```java
@EnableAsync
public class 启动类{}
```

##### 使用

```java
// 使用异步
@Async
public String method(){}
```

## 定时任务

### 开启定时任务

```java
@EnableScheduling
public class 启动类{}
```

### 使用

```java
@Service
public class xxxService{
    // 秒 分 时 日 月 星期
    @Scheduled(cron = "0 * * * * 0-7")
    public String method(){
        System.out.println("执行")
    }
}

```

# 集成

## Banner

```
在resources文件夹下, 放置一个自定义的banner.txt文件
字符画生成网址
http://www.network-science.de/ascii/
```

## Swagger

Swagger用于生成api接口文档, 并可在线测试接口

#### 添加依赖

```xml
<dependency>
     <groupId>io.springfox</groupId>
     <artifactId>springfox-swagger2</artifactId>
     <version>2.9.2</version>
</dependency>
<dependency>
     <groupId>io.springfox</groupId>
     <artifactId>springfox-swagger-ui</artifactId>
     <version>2.9.2</version>
</dependency>
```

#### 添加配置类

```java
~ config.SwaggerConfig.java

@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket docket(){
        // 设置需要使用swagger的环境, dev和test表示application-dev.yml和application-test.yml
        Profiles profiles = Profiles.of("dev", "test");
        // 判断当前是否在自己设定的环境
        // 当当前环境为dev或者test时, 返回true
        boolean flag = environment.acceptsProfiles(profiles);
        
        return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            // 配置多个docket通过groupName来分组
            .groupName("ITianerU")
            // 关闭swagger, 可配置到application.yml中注入
            .enable(flag)
            .select()
            // 配置要扫描接口的方式  RequestHandlerSelectors.basePackage包扫描
            // RequestHandlerSelectors.any() 扫描全部
            // RequestHandlerSelectors.withClassAnnotation(RestController.class) 扫描类上的注解
            // RequestHandlerSelectors.withMethodAnnotation(GetMapping.class) 扫描方法上的注解
            .apis(RequestHandlerSelectors.basePackage("com.itianeru.controller"))
            // 配置路径过滤
            // PathSelectors.ant 扫描指定请求路径的接口
            // PathSelectors.any 全部
            .path(PathSelectors.ant("/user/**"))
            .build();
    }
    // 配置swagger信息
    private ApiInfo apiInfo(){
        // 作者信息
        Contact contact = new Contact("ITianerU","主页地址", "邮箱");
        return new ApiInfo("文档标题", "文档描述", "版本", "组织url", contact, "许可证", "许可证连接",
                           new ArrayList()
        );
        // return new ApiInfoBuilder().build();
    }
}
```

#### 使用

```
项目启动后,访问
启动路径/swagger-ui.html   v2.0访问地址
```

##### controller

```java
@Api(tags = "控制类")
@RestController
public class HelloController{
    @GetMapping("/hello")
    public String hello(){
        return "hello";
    }
    
    @ApiOperation("xxxx接口")
    @GetMapping("/hello2")
    public String hello(@ApiParam("用户名")String Username){
        return "hello" + Username;
    }
}
```

##### 实体类

controller接口中, 如果返回实体类, swagger-ui就会显示该实体类

```java
@ApiModel("用户实体类")
public class User{
    @ApiModelProperty("用户名")
    private String username;
    @ApiModelProperty("年龄")
    private Integer age;
}
```



## log4j

#### 添加依赖

```xml
<!-- log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

#### 添加配置文件

```properties
~ resources/log4j.properties

### set log ###
log4j.rootLogger = INFO, console, infoFile, errorFile
LocationInfo = true

### 配置输出到控制台 ###
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern = [%d{yyyy-MM-dd HH:mm:ss,SSS}]-[%p]:%m   %x  %n


log4j.appender.infoFile = org.apache.log4j.DailyRollingFileAppender
log4j.appender.infoFile.Threshold = INFO
log4j.appender.infoFile.File = /home/logs/log
log4j.appender.infoFile.DatePattern = '.'yyyy-MM-dd'.log'
log4j.appender.infoFile.Append = true
log4j.appender.infoFile.layout = org.apache.log4j.PatternLayout
log4j.appender.infoFile.layout.ConversionPattern = [%d{yyyy-MM-dd HH:mm:ss,SSS}]-[%p]:%m   %x  %n

log4j.appender.errorFile = org.apache.log4j.DailyRollingFileAppender
log4j.appender.errorFile.Threshold = ERROR
log4j.appender.errorFile.File = /home/logs/error
log4j.appender.errorFile.DatePattern = '.'yyyy-MM-dd'.log'
log4j.appender.errorFile.Append = true
log4j.appender.errorFile.layout = org.apache.log4j.PatternLayout
log4j.appender.errorFile.layout.ConversionPattern = [%d{yyyy-MM-dd HH:mm:ss,SSS}]-[%p]:%m   %x  %n
```

## mybatis

#### 添加依赖

```xml
<!-- mybatis -->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.1.0</version>
</dependency>

<!-- mysql -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
```

#### 添加配置

在启动类上添加注解,配置mapper扫描

- @MapperScan("com.itianeru.mango.**.dao")

#### 添加配置

```properties
# 配置数据源
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://ip:3306/mango?useUnicode=true?zeroDateTimeBehavior=convertToNull&autoReconnect=true&characterEncoding=utf-8&serverTimezone=UTC
    username: username
    password: password

# 配置pojo包扫描, mapper.xml扫描
mybayis:
	type-aliases-package: com.itianeru.pojo
	mapper-locations: classpath:mapper/*.xml
```

## mybatis plus

#### 添加依赖

```xml
<!-- mybatis plus -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.3.0</version>
</dependency>
```

#### 代码生成器

```java
// 替换相关参数, 运行该类, 即可生成代码
public class CodeGenerator {
    /**
     * <p>
     * 读取控制台内容
     * </p>
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotEmpty(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/src/main/java");
        gc.setAuthor("ITianerU");
        gc.setOpen(false);
        gc.setSwagger2(true);
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://ip:3306/mango?useUnicode=true&useSSL=false&characterEncoding=utf8");
        // dsc.setSchemaName("public");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("username");
        dsc.setPassword("password");
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName(scanner("请输入你的包名"));
        pc.setParent("com.itianeru.mango");
        mpg.setPackageInfo(pc);

        // 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };

        // 如果模板引擎是 freemarker
        String templatePath = "/templates/mapper.xml.ftl";
        // 如果模板引擎是 velocity
        //String templatePath = "/templates/mapper.xml.vm";

        // 自定义输出配置
        List<FileOutConfig> focList = new ArrayList<>();
        // 自定义配置会被优先输出
        focList.add(new FileOutConfig(templatePath) {
            @Override
            public String outputFile(TableInfo tableInfo) {
                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
                return projectPath + "/src/main/resources/mapper/" + pc.getModuleName()
                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
            }
        });
        /*
        cfg.setFileCreate(new IFileCreate() {
            @Override
            public boolean isCreate(ConfigBuilder configBuilder, FileType fileType, String filePath) {
                // 判断自定义文件夹是否需要创建
                checkDir("调用默认方法创建的目录");
                return false;
            }
        });
        */
        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        // 配置模板
        TemplateConfig templateConfig = new TemplateConfig();

        // 配置自定义输出模板
        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别
        // templateConfig.setEntity("templates/entity2.java");
        // templateConfig.setService();
        // templateConfig.setController();

        templateConfig.setXml(null);
        mpg.setTemplate(templateConfig);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        // 数据库表映射到实体类的命名策略
        strategy.setNaming(NamingStrategy.underline_to_camel);
        // 数据库表字段映射到实体类的命名策略
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);

        // strategy.setSuperEntityClass("com.baomidou.ant.common.BaseEntity");
        // 设置实体类是否为lombok
        strategy.setEntityLombokModel(true);
        // 设置生成RestController控制器
        strategy.setRestControllerStyle(true);
        // 公共父类
        // strategy.setSuperControllerClass("com.baomidou.ant.common.BaseController");
        // 写于父类中的公共字段
        // strategy.setSuperEntityColumns("id");
        // strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        // 驼峰转连字符串
        strategy.setControllerMappingHyphenStyle(true);
        // 设置表前缀
        strategy.setTablePrefix(pc.getModuleName() + "_");
        mpg.setStrategy(strategy);
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());
        mpg.execute();
    }
}
```

#### 分页插件

添加配置

```java
// ~ MyBatisConfig.java
@Bean
public PaginationInterceptor paginationInterceptor(){
	PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
    // 当请求的页超过最大页时, 回到首页
    paginationInterceptor.setOverflow(true);
    // 设置单页最大请求数量
    paginationInterceptor.setLimit(1000);
    return paginationInterceptor;
}
```

使用

```java
@GetMapping("/findPage")
public HttpResult findPage(){
    // 创建分页实体, 构造器第一个参数为页码, 第二个参数为数量
    Page<User> page = new Page<User>(1, 10);
    return HttpResult.ok(userService.page(page))
}
```

#### 多表联查&分页

```java
~ mapper.java
// 分页配置类, 当作该方法的参数, 当调用该方法时, 可自动分页
List<User> findUser(Page<User> page);
```

## CORS 跨域访问

允许服务器跨域访问

```java
~ config/CorsConfig

@Configuration
public class CorsConfig implements WebMvcConfigurer {
    
    public void addCordMapping(CorsRegistry registry){
        // 允许跨域访问的路径
        registry.addMapping("/**")
                // 允许跨域访问的源
                .allowedOrigins("*")
                // 允许访问的方式
                .allowedMethods("POST", "GET", "PUT", "OPTIONS", "DELETE")
                // 预间隔时间
                .maxAge(16800)
                .allowedHeaders("*")
                // 允许头部设置
                .allowCredentials(true);
    }
}
```

## Office操作

#### 添加依赖

```xml
<!-- office -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>4.0.0</version>
</dependency>
```

#### 工具类

```java
~ FileUtils.java
/**
 * 文件相关操作
 */
public class FileUtils {
    /**
     * @Description: 文件下载
     * @Param: response：响应， file:临时文件， newFileName：文件名称
     */
    public static void downloadFile(HttpServletResponse response, File file, String newFileName) {
        try {
            response.setHeader("Content-Disposition", "attachment; filename=" +
                    new String(newFileName.getBytes("ISO-8859-1"), "UTF-8"));
            BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(response.getOutputStream());
            InputStream inputStream = new FileInputStream(file.getAbsolutePath());
            BufferedInputStream bufferedInputStream = new BufferedInputStream(inputStream);
            int length = 0;
            byte[] temp = new byte[1 * 1024 * 10];
            while ((length = bufferedInputStream.read(temp)) != -1){
                bufferedOutputStream.write(temp, 0, length);
            }
            bufferedOutputStream.flush();
            bufferedOutputStream.close();
            bufferedInputStream.close();
            inputStream.close();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

```java
~ PoiUtils.java
/**
 * @Description: office 相关操作
 */
public class PoiUtils {

    /**
     * @Description: 创建excel
     * @Param: workbook： 工作表, fileName： 文件名称
     */
    public static File createExcelFile(Workbook workbook, String fileName){
        OutputStream stream = null;
        File file = null;
        try {
            file = File.createTempFile(fileName, ".xlsx");
            stream = new FileOutputStream(file.getAbsoluteFile());
            workbook.write(stream);
        }catch (FileNotFoundException e){
            e.printStackTrace();
        }catch (IOException e) {
            e.printStackTrace();
        }finally {
            IOUtils.closeQuietly(workbook);
            IOUtils.closeQuietly(stream);
        }
        return file;
    }
}
```

#### 使用(Excel)

```java
public File createUserExcelFile(List<User> list) {
    if(list == null){
        list = new ArrayList<User>();
    }
    // 创建excel工作簿
    Workbook workbook = new XSSFWorkbook();
    // 创建sheet页
    Sheet sheet = workbook.createSheet();
    // 创建第1行
    Row row0 = sheet.createRow(0);
    int columnIndex = 0;
    // 创建单元格,并且赋值表头
    row0.createCell(columnIndex).setCellValue("序号");
    row0.createCell(++columnIndex).setCellValue("ID");
    row0.createCell(++columnIndex).setCellValue("用户名");
    row0.createCell(++columnIndex).setCellValue("昵称");
    row0.createCell(++columnIndex).setCellValue("机构");
    row0.createCell(++columnIndex).setCellValue("角色");
    row0.createCell(++columnIndex).setCellValue("邮箱");
    row0.createCell(++columnIndex).setCellValue("手机号");
    row0.createCell(++columnIndex).setCellValue("状态");
    row0.createCell(++columnIndex).setCellValue("头像");
    row0.createCell(++columnIndex).setCellValue("创建人");
    row0.createCell(++columnIndex).setCellValue("创建时间");
    row0.createCell(++columnIndex).setCellValue("最后更新人");
    row0.createCell(++columnIndex).setCellValue("最后更新时间");
    int rowNum = 0;
    // 遍历数据, 写入表格
    for (User user: list) {
        Row row = sheet.createRow(++rowNum);
        for (int i =0; i < columnIndex + 1; i++){
            row.createCell(i);
        }
        columnIndex = 0;
        row.getCell(columnIndex).setCellValue(rowNum);
        row.getCell(++columnIndex).setCellValue(user.getId());
        row.getCell(++columnIndex).setCellValue(user.getName());
        row.getCell(++columnIndex).setCellValue(user.getNickName());
        row.getCell(++columnIndex).setCellValue(user.getDeptName());
        row.getCell(++columnIndex).setCellValue(user.getRoleNames());
        row.getCell(++columnIndex).setCellValue(user.getEmail());
        row.getCell(++columnIndex).setCellValue(user.getMobile());
        row.getCell(++columnIndex).setCellValue(user.getStatus());
        row.getCell(++columnIndex).setCellValue(user.getAvatar());
        row.getCell(++columnIndex).setCellValue(user.getCreateBy());
        row.getCell(++columnIndex).setCellValue(user.getCreateTime().toString());
        row.getCell(++columnIndex).setCellValue(user.getLastUpdateBy());
        row.getCell(++columnIndex).setCellValue(user.getLastUpdateTime().toString());
    }
    return PoiUtils.createExcelFile(workbook, "user-list");
}
```

## 验证码

#### 添加依赖

```xml
<dependency>
    <groupId>com.github.axet</groupId>
    <artifactId>kaptcha</artifactId>
    <version>0.0.9</version>
</dependency>
```

#### 添加配置类

```java
@Configuration
public class KaptchaConfig {
    
    @Bean
    public DefaultKaptcha producer(){
        Properties properties = new Properties();
        // 边框
        properties.put("kaptcha.border", "no");
        // 字体颜色
        properties.put("kaptcha.textproducer.font.color", "black");
        // 字符间距
        properties.put("kaptcha.textproducer.char.space", "5");
        Config config = new Config(properties);
        DefaultKaptcha defaultKaptcha = new DefaultKaptcha();
        defaultKaptcha.setConfig(config);
        return defaultKaptcha;
    }
}
```

#### 使用

```java
~ LoginController.java

@Autowired
private Producer producer;

@GetMapping("/captcha.jpg")
public void captcha(HttpServletRequest request, HttpServletResponse response) throws IOException {
    response.setHeader("Cache-Control", "no-store, no-cache");
    response.setContentType("image/jpeg");
    // 生成验证码文字
    String text = producer.createText();
    // 生成图片二维码
    BufferedImage image = producer.createImage(text);
    // 将验证码文字保存到session
    request.getSession().setAttribute(Constants.KAPTCHA_SESSION_KEY, text);
    ServletOutputStream out = response.getOutputStream();
    ImageIO.write(image, "jpg", out);
    IOUtils.closeQuietly(out);
}
```

## 安全认证 

### Spring Security

#### 添加依赖

```xml
<!-- thymeleaf配置, 查看使用->模板引擎->依赖-->
<!-- 略 -->
<!-- web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- Spring Security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<!-- jwt -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
<!-- thymeleaf-spring-security 整合包-->
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
    <version>3.0.4.RELEASE</version>
</dependency>
```

#### 使用

```java
package com.itianeru.config
    
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private DataSource dataSource;
    
    // 授权
    @Override
    protected void configure(HttpSecurity http) throws Exception{
        // 首页("/") 允许全部人访问
        http.authorizeRequests().antMatchers("/").permitAll()
            .antMatchers("/路径1/**").hasRole("vip1")   // 配置页面权限
            .antMatchers("/路径2/**").hasRole("vip2")
            .antMatchers("/路径3/**").hasRole("vip3");
        
        // 没有权限跳转到登录页, 该登录页为自带的登录页, 默认会自动访问请求 /login
        http.formLogin()
            // loginPage("/toLogin"); 自定义登录页
            .loginPage("/toLogin")
            // 自定义前端表单登录时传递的参数名称
            .usernameParameter("username").passwordParameter("password")
            // 设置前端表单登录时调用的接口, /login
            // 不设置时, 前端调用的接口与loginPage相同
            .loginProcessingUrl("/login");
        // 开启注销功能  前端调用url /logout
        htto.fromLogout()
           	// 注销时, 清空cookie和session
            .deleteCookies("remove").invalidateHttpSession(false);
        	// 指定注销后, 跳转的页面, 默认是登录页
        	.logoutSuccesssUrl("/toLogin");
        // 关闭csrf功能
        // http.csrf().disable();
        // 开启记住我功能
        http.rememberMe()
            // 自定义前端传递的参数
            .rememberMeParameter("remember");
    }
    
    // 认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception{
        // 给用户配置权限, 会从内存中读
        auth.inMemoryAuthentication()
            .passwordEncoder(new BCryptPasswordEncoder())   // 指定密码加密方式
            .withUser("itianeru")   
            // 密码加密, 授权
            .password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2") 
            .and
            .withUser("root")
            .password(new BCryptPasswordEncoder().encode("123456")).roles("vip1", "vip2", "vip3");
    }
    // 认证, 从数据库z
    @Override
    protected void configureGlobal(AuthenticationManagerBuilder auth) throws Exception{
        UserBuilder users = User.whitDefalutPasswordEncoder();
        // 给用户配置权限, 会从数据库中读
        auth.jdbcAuthentication().dataSource(dataSource).withDafaultSchema()
            .withUser(users.username("itianeru").password("123456").roles("vip1"))   
            .withUser(users.username("root").password("123456").roles("vip1", "vip2", "vip3");
    }     
}
```

#### 前端页面控制权限

```html
<!-- 加入命名空间, thymeleaf-extras-springsecurity4-->
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="https://www.thymeleaf.org/thymeleaf-extras-springsecurity4">
<body>
    <!-- 判断用户是否登录 -->
	<div sec:authorize=="!isAuthenticated()">
        <a th:href="@{/toLogin}">登录</a>
    </div>
    <div sec:authorize=="isAuthenticated()">
        用户名<span sec:authentication="name"></span>
        用户名<span sec:authentication="principal.authorites"></span>
        <a th:href="@{/logout}">注销</a>
    </div>
    
    <!-- 有某个权限才会显示 -->
    <div sec:authorize="hasRole('vip1')">
        内容
    </div>  
</body>
</html>
```

### Shiro

#### 依赖

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.4.1</version>
</dependency>
<!-- configure logging -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jcl-over-slf4j</artifactId>
    <version>1.7.21</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.21</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>runtime</version>
</dependency>
```

#### 配置

resource/shiro.ini

```ini
[users]
root = secret, admin
guest = guest, guest
presidentskroob = 12345, president
darkhelmet = ludicrousspeed, darklord, schwartz
lonestarr = vespa, goodguy, schwartz
[roles]
admin = *
schwartz = lightsaber:*
goodguy = winnebago:drive:eagle5
```

#### 快速使用

未集成springboot

```java
public class Quickstart {
    private static final transient Logger log = LoggerFactory.getLogger(Quickstart.class);

    public static void main(String[] args) {
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");

        SecurityManager securityManager = factory.getInstance();
        SecurityUtils.setSecurityManager(securityManager);

        // 获取当前的用户对象
        Subject currentUser = SecurityUtils.getSubject();

        // 通过当前用户, 去获取shiro session
        Session session = currentUser.getSession();
        // 在session中, 存取值
        session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Retrieved the correct value! [" + value + "]");
        }
		
        // 判断当前的用户是否被认证
        if (!currentUser.isAuthenticated()) {
            // 拿到token
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            // 设置记住我
            token.setRememberMe(true);
            try {
                // 执行登录
                currentUser.login(token);
                // 未知账户异常
            } catch (UnknownAccountException uae) {
                log.info("There is no user with username of " + token.getPrincipal());
                // 密码不正确
            } catch (IncorrectCredentialsException ice) {
                log.info("Password for account " + token.getPrincipal() + " was incorrect!");
                // 用户被锁定
            } catch (LockedAccountException lae) {
                log.info("The account for username " + token.getPrincipal() + " is locked.  " +
                         "Please contact your administrator to unlock it.");
                // 认证异常
            }catch (AuthenticationException ae) {
            }
        }
        // 打印当前用户的认证信息
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");
		
        // 判断用户是否有哪些角色
        if (currentUser.hasRole("schwartz")) {
            log.info("May the Schwartz be with you!");
        } else {
            log.info("Hello, mere mortal.");
        }

        // 判断权限
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        // 判断权限
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  
                     " + "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }

        // 注销
        currentUser.logout();

        System.exit(0);
    }
}


```

#### 集成springboot

##### 依赖

```xml
<!-- thymeleaf配置, 查看使用->模板引擎->依赖-->
<!-- 略 -->
<!-- web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.7.0</version>
</dependency>

<!-- thymeleaf-spring-shiro 整合包-->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

##### 配置

```java
@Configuration
public class ShiroConfig{
    // ShiroFilterBean
    @Bean
    public ShiroFilterBean getShiroFilterBean(
        @Qualifier("securityManager")DefaultWebSecurityManager securityManager){
        ShiroFilterBean shiroFilterBean = new ShiroFilterBean();
        // 设置安全管理器
        shiroFilterBean.setSecurityManager(securityManager);
        // 添加shiro的内置过滤器
        /*
        	anon:无需认证就可以访问
        	authc:必须认证才能访问
        	user:必须拥有记住我功能才能访问
        	perms:拥有对某个资源的权限才能访问
        	role:拥有摸个角色的权限才能访问
        */
        Map<String, String> filterMsp = new LinkedHashMap<>();
        // 配置/user/add该文件的访问权限, 页面跳转需要controller做跳转
        filterMap.put("/user/add", "authc");
        filterMap.put("/user/update", "authc");
        filterMap.put("/user/*", "authc");
        // 有detail权限才能访问
        filterMap.put("/user/detail", "perms[user:detail]");
        shiroFilterBean.setFilterChainDefinitionMap(filterMap);
        
        // 无访问权限跳转到登录页, 登录页需要自己写
        shiroFilterBean.setLoginUrl("/toLogin");
        // 跳转到自定义的未授权页面
        shiroFilterBean.setUnauthorizedUrl("/noauth");
        return shiroFilterBean;
    }
        
    // DefaultWebSecurityManager
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(
        @Qualifier("userRealm")UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 关联Realm
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    // 创建 realm 对象, 需要自定义
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
    
    // 整合thymeleaf
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }
}
```

##### 自定义Realm

```java
public class UserRealm extends AuthorizingRealm{
    // userService 是自定义的service层, 从数据库中取数据
    @Autowired
    private UserServlce userService;
    
    // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals){
       	SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        // 获取当前用户  
    	subject subject = SecurityUtils.getSubject();
        User currentUser = (User)subject.getPrincipal();
        // 添加权限 currentUser.getPerms()获取权限
        info.addStringPermission(currentUser.getPerms());
        return info;
    }
    
    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) 
        throws AuthenticationExcpetion{
        UsernamePasswordToken userToken = (UsernamePasswordToken)token;
        // 从数据库中读取用户名和密码
        User user = userServlce.getUser(userToken.getUsername());
        if(user == null){
            // 抛出异常UnknownAccountException
            return null;
        }
      	// 密码认证, 将数据user放到第一个参数, 可在其他位置获取到该数据
        return new SimpleAuthenticationInfo(user, user.getPassword(),"");
    }
}
```

##### controller

```java
@RequestMapping("/login")
public String login(String username, String password, Model model){
    // 获取当前用户  
    Subject subject = SecurityUtils.getSubject();
    // 获取token
    UsernamePasswordToken token = new UserNamePasswordToken(username, password);
    try{
        subject.login(token);
        return "index";
   	// 用户名不存在异常
    }catch(UnknownAccountException e){
        model.addAttribute("msg", "用户名或密码错误");
        return "login";
    // 密码错误异常
    }catch(IncorrectCredentialsException e){
        model.addAttribute("msg", "用户名或密码错误");
        return "login";
    }
    
}
```

##### 页面

```html
<!-- 加入命名空间, thymeleaf-extras-springsecurity4-->
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:shiro="https://www.thymeleaf.org/thymeleaf-extras-shiro">
<body>
    <!-- 未登录时显示 -->
	<div shiro:guest="true">
        <!-- 略 -->
    </div>
    <!-- 判断是否有某个权限 -->
	<div shiro:hasPermission="user:add">
        <!-- 略 -->
    </div>
</body>
</html>
```



## 数据库连接池-Druid

#### 添加依赖

```xml
<!-- 数据库连接池 druid-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.20</version>
</dependency>
```

#### 添加配置

```properties
# 配置数据源, 数据库连接池
spring:
  datasource:
    name: druidDataSource
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://152.136.157.189:3306/mango?useUnicode=true?zeroDateTimeBehavior=convertToNull&autoReconnect=true&characterEncoding=utf-8
      username: root
      password: Lrt123456#
#      filters: stat,wall,log4j, config  # 配置监控统计拦截的filters, 去掉后监控界面SQL无法统计, wall用于防火墙
      max-active: 100  # 最大连接数
      initial-size: 1  # 初始化大小
      max-wait: 60000 # 最大等待时间
      min-idle: 1  # 最小连接数
      time-between-eviction-runs-millis: 60000 # 间隔多久检测一次要关闭的空闲连接
      min-evictable-idle-time-millis: 300000 # 一个连接在连接池中最小的生存时间
      validation-query: 'SELECT 1 FROM DUAL' # 验证数据库连接的查询语句
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      pool-prepared-statements: true  # 缓存
      max-open-prepared-statements: 50
      max-pool-prepared-statement-per-connection-size: 20
```

#### 添加配置类

```java
@Configuration
public class DruidConfig {
    
    @ConfigurationProperties(prefix = "spring,datasource.druid")
    @Bean
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }
	
    // 配置后台监控
    @Bean
    @ConditionalOnMissingBean
    public ServletRegistrationBean<Servlet> statViewServlet() {
        ServletRegistrationBean<Servlet> servletRegistrationBean = new ServletRegistrationBean<Servlet>(new StatViewServlet(), "/druid/*");
        // 添加IP白名单, 是访问的客户端ip
        servletRegistrationBean.addInitParameter("allow", "127.0.0.1,192.168.42.74");
        // 添加IP黑名单，当白名单和黑名单重复时，黑名单优先级更高
        // servletRegistrationBean.addInitParameter("deny", "192.168.25.123");
        // 添加控制台管理用户
        servletRegistrationBean.addInitParameter("loginUsername", "admin");
        servletRegistrationBean.addInitParameter("loginPassword", "admin");
        // 是否能够重置数据
        servletRegistrationBean.addInitParameter("resetEnable", "false");
        return servletRegistrationBean;
    }

    /**
     * 配置服务过滤器
     *
     * @return 返回过滤器配置对象
     */
    @Bean
    @ConditionalOnMissingBean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(new WebStatFilter());
        // 添加过滤规则
        filterRegistrationBean.addUrlPatterns("/*");
        // 忽略过滤格式
        filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*,");
        return filterRegistrationBean;
    }
}
```

#### 访问监控页面

```
http://ip:port/druid/login.html
```

## 监控服务

#### 添加依赖

```xml
# 新建项目, 添加如下依赖 (推荐在创建项目时, 选择依赖包后再创建)
<!-- 监控服务 -->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
</dependency>
```

```xml
# 在想要监控的服务上添加依赖
<!-- 监控客户端 -->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.2.1</version>
</dependency>
```

#### 添加配置

```yml
# 监控服务配置
server:
  port: 8000 # 设置端口号

spring:
  application:
    name: xxx # 设置监控服务的名称
```

注册服务

```yml
# 客户端配置
spring:		
    boot:
        admin:
          client:
            url: "http://localhost:8000"  # 指定监控服务器地址, 将该服务注册到监控服务
            
# 暴露端点, 不然无法被监控服务器查看
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

#### 开启监控服务

```java
@SpringBootApplication
@EnableAdminServer   // 在监控服务的启动类添加@EnableAdminServer注解, 开启监控服务
public class MangoMonitorApplication {
    public static void main(String[] args) {
        SpringApplication.run(MangoMonitorApplication.class, args);
    }
}
```

#### 使用

```
访问http://localhost:8000, 可看到被监控的服务
```

## 邮件

### 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

### 配置

```yml
spring:
	mail:
		username: 邮件地址
		password: 授权码
		host: smtp.163.com
		properties: 
			mail:
				smtl:
					ssl:
						enable: true # 开启加密验证
```

### 使用

```java
@Service
public class EmailService{
    @AutoWired
    private JavaMailSenderImpl mailSender;
    
    public void sendTest(){
        // 简单邮件
        SimpleMaillMessage msg = new SimpleMaillMessage ();
        msg.setSubject("标题");
        msg.setText("内容");
        msg.setTo("目标邮箱");
        msg.setFrom("从哪个邮箱发送");
        mailSender.send(msg);
    }
    
    public void sendTest(){
        // 复杂邮件
        MimeMessage msg = new MimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(msg, true);
        helper.setSubject("标题");
        helper.setText("<p>html文本</p>", true); // true开启html
        helper.addAttachment("附件名", new File("附件路径"));  // 添加附件, 可添加多个
        helper.setTo("目标邮箱");
        helper.setFrom("从哪个邮箱发送");
        mailSender.send(msg);
    }
}
```

## Redis

### 说明

springboot在2.x之后, jedis被替换为lettuce, lettuce性能更高

**jedis:** 采用直连, 多线程操作不安全, 想要避免不安全, 需要使用jedis pool连接池, 像BIO模式

**lettuce:**  采用netty, 实例可以在多个线程之间共享, 线程安全, 可减少线程数据, 像nio模式 

### 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 配置

```yml
spring:
	redis: 
		host: 地址
		port: 6379		
```

### 使用

```java
@Service
private class xxxService{
    @Autowired
    private RedisTemplate redisTemplate;
    
    public void method(){
        // opsForValue() 操作字符串
        // opsForList() 操作list
        redisTemplate.opsForValue().set("key", "value")
    }
}
```

#### 自定义redisTemplate

```java
@Configuration
public class RedisConfig{
    @Bean
    public RedisTemplate<String, Object> 
        redisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate<String, Object> template = new RedisTemplate();
        // Jackson序列化
        Jackson2JsonRedisSerializer<Obekct> serializer = new Jackson2JsonRedisSerializer<>();
        ObjectMapper om = new ObjectMapper();
        om.setVisivility(PropertyAccessor.All, JsonAutoDetect.Visivility.ANY);
        om.enableDefaultTyping(ObjectMapper.defaultTyping.NON_FINAL);
        serializer.setObjectMapper(om);
        // string序列化
        StringRedisSerializer stringSerializer = new StringRedisSerializer<>();
     	// 设置序列化方式
        template.setKeySerializer(stringSerializer);
        template.setValueSerializer(serializer);
        template.setHashKeySerializer(stringSerializer);
        template.setHashValueSerializer(serializer);
        template.afterPropertiesSet();
        // 设置连接工厂
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```

#### RedisUtils

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





