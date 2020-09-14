# mybatis

## 依赖

```xml
<dependency>
	<groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.46</version>
</dependency>
<dependency>
	<groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```



## 配置文件

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://ip:3306/databaseName?useSSL=true&useUnicode=true&characterEncodingUTF-8
username=root
password=123456
```

在resources下创建mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 读取配置文件 -->
    <properties resource="db.properties">
        <!-- 内部定义属性值, 优先级没有外部引入的高 -->
    	<property name="username" value="root"></property>
    </properties>
	<!-- default选择环境, 每次只能使用一个 -->
    <environments default="development">
        <!-- 可配置多个environment, 连接不同的数据库 oracle/mysql等 -->
        <environment id="development">
            <!-- transactionManager是事务管理器可选择JDBC或者MANAGED, 配置成MANAGED没有事务 -->
            <!-- 使用spring + mybatis时, 无需配置transactionManager, spring会使用自带的管理器覆盖配置 -->
            <transactionManager type="JDBC"/>
            <!-- type可选值 UNPOOLED(无连接池)|POOLED(有连接池)|JNDI(在EJB等容器中使用, 不常用) -->
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <settings>
        <!-- 开启缓存（二级缓存）-->
        <setting name="cacheEnabled" value="true"/>
        <!-- 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。默认 false  -->
        <setting name="lazyLoadingEnabled" value="true"/>
        <!-- 开启驼峰命名映射, a_b 映射 aB-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!-- 指定mybatis所用的日志, 未指定时自动查找 -->
        <setting name="logImpl" value="STDOUT_LOGGING" />
        <setting name="localCacheScope" value="SESSION"/>
    </settings>
    <typeAliases>
        <!-- 类别名, 可以在xml中的resultType和parameterType直接使用别名-->
        <typeAlias alias="user" type="com.itianeru.pojo.User" />
        <!-- 也可以扫描包, 默认的别名是实体类首字母小写的名称 -->
        <!-- 也可以在实例类上使用 @Alias("别名")指定别名, 在resultType和parameterType中使用基本类型, 需要在前面加下划线, 如 _int, _double, 不加下划线, 是指包装类, 其他的常用集合,只要首字母小写即可如, map, hashMap, arrayList等 -->
        <package name="com.itiianeru.pojo">
    </typeAliases>
    <!-- 映射器 -->
    <mappers>
        <mapper resource="mapper/*.xml"/>
        <!-- 接口映射, 需要xml和接口在同一目录下, 并且名称要相同 UserMapper.class 和UserMapper.xml -->
        <mapper class="com.itianeru.mapper.UserMapper"/>
        <package name="com.itianeru.mapper">
    </mappers>
</configuration>
```

## 获取sqlSessionFactory

```java
public class MybatisUtils{
    // 程序开始运行就创建, 程序结束才销毁
    private static SqlSessionFactory sqlSessionFactory
    static{
        try{
            String resource = "mybatis-config.xml";
            InputStream is = Resources.getResourceAsStream(resouce);
            // SqlSessionFactoryBuilder()在创建sqlSessionFactory后, 就不再需要
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
        }catch(IOException e){
            e.printStackTrace();
        }
    }
    
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}

```

## 获取sqlSession

```java
SqlSession sqlSession = sqlSession = MybatisUtils.getSqlSession();
try{
    // UserDao是与mapper.xml映射的类
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    userMapper.selectAll();
    // 编辑或者新增需要提交事务
    sqlSession.commit();
}finally{
    sqlSession.rollback();
    sqlSession.close();
} 
```

## 结果集映射

```java
public class User(){
    private int id;
    private String username;
    private Job job;
}

public class Job(){
    private int id;
    private String jobName;
}

public class User2(){
    private int id;
    private String username;
    private List<Job> list;
}
```



```xml
<resultMap id="userResultMap" type="User">
	<result property="id" column="id"  />
    <result property="username" column="username"  />
    <!-- 
		复杂的属性, 单独处理 
		对象:  association
		集合:  collection
	-->
    <!-- 方式一: 子查询, 延时加载 -->
    <!-- 方式一: column指的是 result对应的字段, 当做变量传参-->
    <association property="job"  column="id" javaType="Job" select="getJob"/>
    <!-- 方式二: 直接映射复杂的数据类型 -->
    <association property="job"  javaType="Job">
        <result property="id" column="id"/>
        <result property="jobName" column="job_name"/>
    </association>
    
    <!-- 方式一:  集合映射 -->
    <collection property="list" ofType="Job">
        <result property="id" column="id"/>
        <result property="jobName" column="job_name"/>
    </collection>
    <!-- 方式一:  子查询, 延时加载 -->
    <collection property="list" javaType="ArrayList" ofType="Job" select="getJob" column="id"/>
      
</resultMap>

<select id="getUserList" resultMap="userResultMap">
	select * from User
</select>

<select id="getJob" resultType="Job">
	select * from Job where id = #{id}
</select>
```



## 查询

### 模糊查询

**方法一** 

用java代码, 拼接%, 再传给mybatis

**方法二** 

oracle不支持, mysql可以

```sql
where 字段名 like "%"#{字段名}"%"
```

**方法三**

```sql
where 字段名 like CONCAT('%', #{字段名} ,'%')
```

### 懒加载

#### 开启

```xml
<settings>
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="false"></setting>
</settings>
```

#### 使用

查看上方结果集映射的xml

```java
// 此时只会执行, select * from User 这条sql
List<User> list = userMapper.getUserList();
for(User user : list){
    // 当要获取, job的值时, 才会执行 select * from Job where id = #{id} 这个条sql, 去查询对应的job
    System.out.println(user.getList())
}
```

## 日志

在<settings>配置<setting name="logImpl" value="sli4j" />

- STDOUT_LOGGING

  可直接使用

- SLF4J

- LOG4J

  详细内容在log.md笔记中

  - 先导入log4j依赖
  - 配置文件
  
- 等

## 分页

### limit分页

## 动态sql

### where/set

where动态添加and

set动态管理update语句中的逗号

```xml
<update id="" parameterType="map">
    update User
    <set>
        <if test="id != null ">
            age = #{age},
        </if>
        <if test="username != null ">
            username = #{username},
        </if>
    </set>
    where id = #{id}
<update>
```

### if

```xml
<select id="" parameterType="map">
    select * from User
    <where>
        <if test="id != null ">
            id = #{id}
        </if>
        <if test="username != null ">
            and username = #{username}
        </if>
    </where>
<select>
```

### choose

只选择其中一个 

```xml
<select id="" parameterType="map">
    select * from User
    <where>
        <choose>
            <when test="id != null ">
            	id = #{id}
            </when>
            <when test="username != null ">
                and username = #{username}
            </when>
            <otherwise>
            	and age = #{age}
            </otherwise>
        </choose>
    </where>
<select>
```

### trim

自定义前缀添加的语句和动态添加的字符

```xml
<!-- 前缀添加where, 前坠动态管理and 或 or -->
<trim prefix="where" prefixOverrides="AND | OR">
</trim>
<!-- 前坠添加set, 后缀动态管理, -->
<trim prefix="set" suffixOverrides=",">
</trim>
```

### foreach

```xml
<select id="" parameterType="map" > 
	select * from user 
    <where>
        <!-- collection中的集合是map传递的 -->
        <!-- open是前坠, close是后缀, separator是分隔符, item是遍历的值 -->
        id in
        <foreach collection="ids" item="id" open=" (" close=")" separator=",">
			#{id}
        </foreach>
    </where>
</select>
```



## sql片段

可重复使用代码, 不要将<where>放到<sql>中

```xml
<sql id="sqlpd">
	username, age
</sql>

<select>
    select <include refid="sqlpd"></include> from user
</select>
```

## 缓存

存在内存中的临时数据, 将用户经常查询的数据, 放到内存中, 提升查询效率.

经常查询, 不经常修改的数据使用缓存

### 一级缓存

默认开启, 在同一个sqlSession下, 同一个查询会读取上一个查询的缓存数据, 不会重复查询数据库.

- 语句中有增删改会刷新缓存
- 不同的入参会刷新缓存
- 缓存不会定时刷新
- 缓存会保存列表或者对象的1024个引用
- 缓存会使用LRU算法,清除缓存

### 二级缓存

需要手动开启, 基于namespace级别的缓存, 就是说一个命名空间对应一个缓存

##### 缓存策略

LRU(默认)  最近最少使用: 移除最长时间不被使用的对象.

FIFO  先进先出: 按对象进入缓存的顺序来移除它们.

SOFT 软引用: 基于垃圾回收器的状态和软引用规则移除对象.

WEAK 弱引用: 更积极的基于垃圾回收器的状态和软弱用规则移除对象.

### 工作机制

- 一个会话(sqlSession)查询到一条数据, 会放到当前会话一级缓存中
- 当前会话关闭, 一级缓存中的数据被保存到二级缓存中

### 开启二级缓存

```xml
<!-- 默认就是开启的 -->
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```

```xml
<!-- 在mapper文件中-->
<mapper namespace="">
    <!-- 开启当前namespace的二级缓存  eviction缓存策略 flushInterval指定时间刷新缓存 size缓存的数量 readOnly是否只读, 为false时, 需要实体类实现序列化接口, 为true性能更高, 为false时更安全-->
	<cache eviction="FIFO" flushInterval="60000" size="500" readOnly="true"/>
    <!-- 也可使用默认设置 -->
    <cache/>
    <!-- 可以指定select不使用二级缓存 -->
    <select id="" userCache="false"></select>
</mapper>
```

### 自定义缓存

#### ehcache

##### 依赖

```xml
<dependency>
	<groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.1.0</version>
</dependency>
```

##### 配置

ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 		
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
          updateCheck="false">
	<!-- diskStore: 为缓存路径, ehcache分为内存和磁盘两级.此属性定义磁盘的缓存位置。参数解释如下:
		user.home - 用户主目录
		user.dir - 用户当前工作目录
		java.io.tmpdir -默认临时文件路径
	-->
	<diskStore path="./tmpdip/Tmp_EhCache"/>
    <defaultCache
            eternal="false"
            maxElementsInMemory="10000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="259200"
            memoryStoreEvictionPolicy="LRU" />
    <cache
            name="cloud_user"
            eternal="false"
            maxElementsInMemory="5000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="1800"
            memoryStoreEvictionPolicy="LRU" />
    <!-- 
            name:缓存名称。
            maxELementsInMemory: 缓存最大数目
            maxELementsOnDisk:硬盘最大缓存个数。
            eternal: 对象是否永久有效.一但设置了timeout将不起作用。
            overfiowToDisk: 是否保存到磁盘, 当系统宕机时
            timeToIdLeSeconds:设置对象在失效前的允许闲置的时间（单位：秒）. 仅当eternaL=false对象不是永久有效时使用, 可选属性.默认值是0, 
            timeToLiveSeconds:设置对象在失效前的允许存活的时间（单位：秒）. 最大时间介于创建时间和失效时间之间. 仅当eternaL=false对象不是永久有效时使用
            diskPersistent:是否缓存虚拟机重启期数据 
            diskSpooLBufferSizeMB:这个参数设置DiskStore (磁啟缓存）的缓存区大小。默认足3QMB。每个Cache都应该有一个自己的缓沖区。 
            diskExpiryThreadlrrtervaLSeconds:磁盘失效线程运行时间间隔，默认是120秒
            memoryStoreEvictionPoLicy: 当达到maxELementsInMemory限制时• Ehcache将会根据指定的策略去清理内存.默认策略是LRU(最近最少使用). 
            cLearOnFLush:内存数量最大时是否清除。
            memoryStoreEvictionPoLicy: 可选策略有: LRU (最近最少使用丨默认策略）、FIFO (先进先出）、LFU (最少访问次数）。
	-->
</ehcache>
```



##### 使用

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache">
```



# 代码生成器

## 插件安装

在maven配置中添加

```xml
~ pom.xml
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.4.0</version>
    <executions>
        <execution>
            <id>Generate MyBatis Artifacts</id>
            <goals>
                <goal>generate</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <!-- 输出详细信息 -->
        <verbose>true</verbose>
        <!-- 覆盖生成文件 -->
        <overwrite>true</overwrite>
        <!-- 定义配置文件 -->
        <configurationFile>/src/main/resources/generator-configuration.xml</configurationFile>
    </configuration>
</plugin>
```

## 配置文件

在resources目录下新建generator-configuration.xml文件

```xml
~ generator-configuration.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!--<properties resource="db.properties"></properties>-->
    <!--配置数据库连接信息-->
    <context id="default" targetRuntime="MyBatis3">
        <!--不让生成注释-->
        <commentGenerator>
            <property name="suppressAllComments" value="true"></property>
        </commentGenerator>
        <!--idea 将&当成了特殊符号 这里需要转义 & = &amp;-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/demo?useUnicode=true?zeroDateTimeBehavior=convertToNull&amp;autoReconnect=true&amp;characterEncoding=utf-8"
                        userId="root"
                        password="123456">
        </jdbcConnection>

        <javaTypeResolver >
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>
        <!--指定javabean的生成位置-->
        <javaModelGenerator targetPackage="com.kd.yxcd.entity" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!--指定sql映射文件的位置-->
        <sqlMapGenerator targetPackage="mapper"  targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!--指定dao接口的生成位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.kd.yxcd.mapper"  targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <table tableName="ev_equipment_order" domainObjectName="EquipmentOrder">
            <property name="useActualColumnNames" value="false" />
        </table>
    </context>
</generatorConfiguration>
```

### 配置文件详解

- **context**

  - **属性**

    - **id**：`Context`示例的唯一`ID`，用于输出错误信息时候作为唯一标记。

    - **targetRuntime**：用于执行代码生成模式。

      |          属性          |                           功能描述                           |
      | :--------------------: | :----------------------------------------------------------: |
      |  `MyBatis3DynamicSql`  | 默认值，兼容`JDK8+`和`MyBatis 3.4.2+`，不会生成`XML`映射文件，忽略`<sqlMapGenerator>`的配置项，也就是`Mapper`全部注解化，依赖于`MyBatis Dynamic SQL`类库 |
      |    `MyBatis3Kotlin`    |  行为类似于`MyBatis3DynamicSql`，不过兼容`Kotlin`的代码生成  |
      |       `MyBatis3`       | 提供基本的基于动态`SQL`的`CRUD`方法和`XXXByExample`方法，会生成`XML`映射文件 |
      |    `MyBatis3Simple`    |   提供基本的基于动态`SQL`的`CRUD`方法，会生成`XML`映射文件   |
      | `MyBatis3DynamicSqlV1` |                     已经过时，不推荐使用                     |

  - **标签**

    - **jdbcConnection** :  指定数据源的连接信息

      |      属性       |       功能描述       | 是否必须 |
      | :-------------: | :------------------: | :------: |
      |  `driverClass`  |  数据源驱动的全类名  |   `Y`    |
      | `connectionURL` |  `JDBC`的连接`URL`   |   `Y`    |
      |    `userId`     | 连接到数据源的用户名 |   `N`    |
      |   `password`    |  连接到数据源的密码  |   `N`    |

    - **commentGenerator**:   控制生成的实体的注释内容

      |     property属性      |                   功能描述                   |           默认值            |
      | :-------------------: | :------------------------------------------: | :-------------------------: |
      | `suppressAllComments` |                 是否生成注释                 |           `false`           |
      |    `suppressDate`     |         是否在注释中添加生成的时间戳         |           `false`           |
      |     `dateFormat`      | 配合`suppressDate`使用，指定输出时间戳的格式 | `java.util.Date#toString()` |
      |  `addRemarkComments`  |        是否输出表和列的`Comment`信息         |           `false`           |

    - **javaTypeResolver**:  用于解析和计算数据库列类型和`Java`类型的映射关系

      |    property属性    |                           功能描述                           | 默认值  |
      | :----------------: | :----------------------------------------------------------: | :-----: |
      | `forceBigDecimals` | 是否强制把所有的数字类型强制使用`java.math.BigDecimal`类型表示 | `false` |
      |  `useJSR310Types`  |         是否支持`JSR310`，主要是`JSR310`的新日期类型         | `false` |

      如果`useJSR310Types`属性设置为`true`，那么生成代码的时候类型映射关系如下（主要针对日期时间类型）

      |    数据库（JDBC）类型     |          Java类型          |
      | :-----------------------: | :------------------------: |
      |          `DATE`           |   `java.time.LocalDate`    |
      |          `TIME`           |   `java.time.LocalTime`    |
      |        `TIMESTAMP`        | `java.time.LocalDateTime`  |
      |   `TIME_WITH_TIMEZONE`    |   `java.time.OffsetTime`   |
      | `TIMESTAMP_WITH_TIMEZONE` | `java.time.OffsetDateTime` |

    - **javaModelGenerator** : 主要用于控制实体（`Model`）类的代码生成行为

      **属性**

      |      属性       |                  功能描述                  | 是否必须 |            备注            |
      | :-------------: | :----------------------------------------: | :------: | :------------------------: |
      | `targetPackage` |             生成的实体类的包名             |   `Y`    | 例如`club.throwable.model` |
      | `targetProject` | 生成的实体类文件相对于项目（根目录）的位置 |   `Y`    |    例如`src/main/java`     |

      **标签**

      |      property属性      |                          功能描述                           | 默认值  |                             备注                             |
      | :--------------------: | :---------------------------------------------------------: | :-----: | :----------------------------------------------------------: |
      |   `constructorBased`   |           是否生成一个带有所有字段属性的构造函数            | `false` |             `MyBatis3Kotlin`模式下忽略此属性配置             |
      |  `enableSubPackages`   |                是否允许通过`Schema`生成子包                 | `false` | 如果为`true`，例如包名为`club.throwable`，如果`Schema`为`xyz`，那么实体类文件最终会生成在`club.throwable.xyz`目录 |
      | `exampleTargetPackage` |             生成的伴随实体类的`Example`类的包名             |    -    |                              -                               |
      | `exampleTargetProject` | 生成的伴随实体类的`Example`类文件相对于项目（根目录）的位置 |    -    |                              -                               |
      |      `immutable`       |                         是否不可变                          | `false` | 如果为`true`，则不会生成`Setter`方法，所有字段都使用`final`修饰，提供一个带有所有字段属性的构造函数 |
      |      `rootClass`       |                   为生成的实体类添加父类                    |    -    |               通过`value`指定父类的全类名即可                |
      |     `trimStrings`      |       `Setter`方法是否对字符串类型进行一次`trim`操作        | `false` |                              -                               |

    - **javaClientGenerator**:   主要用于控制`Mapper`接口的代码生成行为

      **属性**

      |      属性       |                     功能描述                     | 是否必须 |                             备注                             |
      | :-------------: | :----------------------------------------------: | :------: | :----------------------------------------------------------: |
      |     `type`      |               `Mapper`接口生成策略               |   `Y`    | `<context>`标签的`targetRuntime`属性为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时此属性配置忽略 |
      | `targetPackage` |             生成的`Mapper`接口的包名             |   `Y`    |                 例如`club.throwable.mapper`                  |
      | `targetProject` | 生成的`Mapper`接口文件相对于项目（根目录）的位置 |   `Y`    |                     例如`src/main/java`                      |

      `type`属性的可选值如下：

      - `ANNOTATEDMAPPER`：`Mapper`接口生成的时候依赖于注解和`SqlProviders`（也就是纯注解实现），不会生成`XML`映射文件。
      - `XMLMAPPER`：`Mapper`接口生成接口方法，对应的实现代码生成在`XML`映射文件中（也就是纯映射文件实现）。
      - `MIXEDMAPPER`：`Mapper`接口生成的时候复杂的方法实现生成在`XML`映射文件中，而简单的实现通过注解和`SqlProviders`实现（也就是注解和映射文件混合实现）。

      注意两点：

      - `<context>`标签的`targetRuntime`属性指定为`MyBatis3Simple`的时候，`type`只能选用`ANNOTATEDMAPPER`或者`XMLMAPPER`。
      - `<context>`标签的`targetRuntime`属性指定为`MyBatis3`的时候，`type`可以选用`ANNOTATEDMAPPER`、`XMLMAPPER`或者`MIXEDMAPPER`。

      **标签**

      |    property属性     |              功能描述              | 默认值  |                             备注                             |
      | :-----------------: | :--------------------------------: | :-----: | :----------------------------------------------------------: |
      | `enableSubPackages` |    是否允许通过`Schema`生成子包    | `false` | 如果为`true`，例如包名为`club.throwable`，如果`Schema`为`xyz`，那么`Mapper`接口文件最终会生成在`club.throwable.xyz`目录 |
      | `useLegacyBuilder`  | 是否通过`SQL Builder`生成动态`SQL` | `false` |                                                              |
      |   `rootInterface`   |   为生成的`Mapper`接口添加父接口   |    -    |              通过`value`指定父接口的全类名即可               |

    - **sqlMapGenerator**:   主要用于控制`XML`映射文件的代码生成行为

      **属性**

      |      属性       |                   功能描述                    | 是否必须 |           备注           |
      | :-------------: | :-------------------------------------------: | :------: | :----------------------: |
      | `targetPackage` |           生成的`XML`映射文件的包名           |   `Y`    |      例如`mappings`      |
      | `targetProject` | 生成的`XML`映射文件相对于项目（根目录）的位置 |   `Y`    | 例如`src/main/resources` |

      **标签**

      |    property属性     |           功能描述           | 默认值  | 备注 |
      | :-----------------: | :--------------------------: | :-----: | :--: |
      | `enableSubPackages` | 是否允许通过`Schema`生成子包 | `false` |  -   |

    - **plugin**:    引入一些插件对代码生成的一些特性进行扩展

      | 属性 |                        功能描述                        |                             备注                             |
      | :--: | :----------------------------------------------------: | :----------------------------------------------------------: |
      | type | 用于指定`org.mybatis.generator.api.Plugin`接口的实现类 | 例如：引入`org.mybatis.generator.plugins.SerializablePlugin` 插件会让生成的实体类自动实现`java.io.Serializable`接口并且添加`serialVersionUID`属性。 |

    - **table**:   主要用于配置要生成代码的数据库表格，定制一些代码生成行为等等

      **属性**

      | `tableName`                 | 数据库表名称                                          | `Y`  | 例如`t_order`                                                |
      | --------------------------- | ----------------------------------------------------- | ---- | ------------------------------------------------------------ |
      | `schema`                    | 数据库`Schema`                                        | `N`  | -                                                            |
      | `catalog`                   | 数据库`Catalog`                                       | `N`  | -                                                            |
      | `alias`                     | 表名称标签                                            | `N`  | 如果指定了此值，则查询列的时候结果格式为`alias_column`       |
      | `domainObjectName`          | 表对应的实体类名称，可以通过`.`指定包路径             | `N`  | 如果指定了`bar.User`，则包名为`bar`，实体类名称为`User`      |
      | `mapperName`                | 表对应的`Mapper`接口类名称，可以通过`.`指定包路径     | `N`  | 如果指定了`bar.UserMapper`，则包名为`bar`，`Mapper`接口类名称为`UserMapper` |
      | `sqlProviderName`           | 动态`SQL`提供类`SqlProvider`的类名称                  | `N`  | -                                                            |
      | `enableInsert`              | 是否允许生成`insert`方法                              | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableSelectByPrimaryKey`  | 是否允许生成`selectByPrimaryKey`方法                  | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableSelectByExample`     | 是否允许生成`selectByExample`方法                     | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableUpdateByPrimaryKey`  | 是否允许生成`updateByPrimaryKey`方法                  | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableDeleteByPrimaryKey`  | 是否允许生成`deleteByPrimaryKey`方法                  | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableDeleteByExample`     | 是否允许生成`deleteByExample`方法                     | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableCountByExample`      | 是否允许生成`countByExample`方法                      | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `enableUpdateByExample`     | 是否允许生成`updateByExample`方法                     | `N`  | 默认值为`true`，执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `selectByPrimaryKeyQueryId` | `value`指定对应的主键列提供列表查询功能               | `N`  | 执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `selectByExampleQueryId`    | `value`指定对应的查询`ID`提供列表查询功能             | `N`  | 执行引擎为`MyBatis3DynamicSql`或者`MyBatis3Kotlin`时忽略此配置 |
      | `modelType`                 | 覆盖`<context>`的`defaultModelType`属性               | `N`  | 见`<context>`的`defaultModelType`属性                        |
      | `escapeWildcards`           | 是否对通配符进行转义                                  | `N`  | -                                                            |
      | `delimitIdentifiers`        | 标记匹配表名称的时候是否需要使用分隔符去标记生成的SQL | `N`  | -                                                            |
      | `delimitAllColumns`         | 是否所有的列都添加分隔符                              | `N`  | 默认值为`false`，如果设置为`true`，所有列名会添加起始和结束分隔符 |

      **标签**

      |        property属性         |                          功能描述                          | 默认值  |                             备注                             |
      | :-------------------------: | :--------------------------------------------------------: | :-----: | :----------------------------------------------------------: |
      |     `constructorBased`      |         是否为实体类生成一个带有所有字段的构造函数         | `false` |          执行引擎为`MyBatis3Kotlin`的时候此属性忽略          |
      | `ignoreQualifiersAtRuntime` |                    是否在运行时忽略别名                    | `false` | 如果为`true`，则不会在生成表的时候把`schema`和`catalog`作为表的前缀 |
      |         `immutable`         |                      实体类是否不可变                      | `false` |          执行引擎为`MyBatis3Kotlin`的时候此属性忽略          |
      |         `modelOnly`         |                     是否仅仅生成实体类                     | `false` |                              -                               |
      |         `rootClass`         |         如果配置此属性，则实体类会继承此指定的超类         |   `-`   |             如果有主键属性会把主键属性在超类生成             |
      |       `rootInterface`       |         如果配置此属性，则实体类会实现此指定的接口         |   `-`   | 执行引擎为`MyBatis3Kotlin`或者`MyBatis3DynamicSql`的时候此属性忽略 |
      |      `runtimeCatalog`       |                   指定运行时的`Catalog`                    |   `-`   | 当生成表和运行时的表的`Catalog`不一样的时候可以使用该属性进行配置 |
      |       `runtimeSchema`       |                    指定运行时的`Schema`                    |   `-`   | 当生成表和运行时的表的`Schema`不一样的时候可以使用该属性进行配置 |
      |     `runtimeTableName`      |                     指定运行时的表名称                     |   `-`   | 当生成表和运行时的表的表名称不一样的时候可以使用该属性进行配置 |
      |  `selectAllOrderByClause`   |  指定字句内容添加到`selectAll()`方法的`order by`子句之中   |   `-`   |         执行引擎为`MyBatis3Simple`的时候此属性才适用         |
      |        `trimStrings`        |            实体类的字符串类型属性会做`trim`处理            |   `-`   |          执行引擎为`MyBatis3Kotlin`的时候此属性忽略          |
      |   `useActualColumnNames`    |               是否使用列名作为实体类的属性名               | `false` |                              -                               |
      |     `useColumnIndexes`      | `XML`映射文件中生成的`ResultMap`使用列索引定义而不是列名称 | `false` | 执行引擎为`MyBatis3Kotlin`或者`MyBatis3DynamicSql`的时候此属性忽略 |
      | `useCompoundPropertyNames`  |         是否把列名和列备注拼接起来生成实体类属性名         | `false` |                              -                               |

  