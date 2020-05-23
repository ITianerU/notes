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

  