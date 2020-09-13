# LOG4J

## 依赖

```xml
<dependency>
	<groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

## 配置

log4j.properties

```properties
# 林将等级为OEBUG的日志信息输出到consoLe和file这两个目的地.console和fiLe的定义在下面的代码
log4j.rootLogger = DEBUG,console,file

# 控制台输出的相关设罝
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold = DEBUG
log4j.appender.console.layout = org.apache.log4j,PatternLayout
log4j.appender.console.layout.ConversionPattern = [%c]-%m%n

# 特文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
# 文件地址
log4j.appender.file.File=./log/itianeru.log 
log4j.appender.file.MaxFileSize=10mb 
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

# 日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j•logger•java•sql•Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

## 使用

```java
public class UserService{
    private final static Logger log = Logger.getLogger(UserService.class);
    
    // 提示
    log.info();
    // 调试信息
    log.dubug();
    // 错误信息
    log.error();
}
```



