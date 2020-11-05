#### 1.maven 依赖

```xml
 <dependency>
     <groupId>com.baomidou</groupId>
     <artifactId>mybatis-plus-boot-starter</artifactId>
     <version>3.3.2</version>
</dependency>

<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.2</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
 </dependency>
```

#### 2.逆向工程代码

```java
package com.example;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        //创建对象
        AutoGenerator autoGenerator = new AutoGenerator();
        //数据源
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/takeout");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("123456");
        autoGenerator.setDataSource(dataSourceConfig);
        //全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOutputDir(System.getProperty("user.dir")+"/product-service/src/main/java");
        globalConfig.setAuthor("southwind");
        globalConfig.setOpen(false);
        //去掉Service的I
        globalConfig.setServiceName("%sService");
        autoGenerator.setGlobalConfig(globalConfig);
        //包配置
        PackageConfig packageConfig = new PackageConfig();
        packageConfig.setParent("com.southwind");
        packageConfig.setEntity("entity");
        packageConfig.setMapper("mapper");
        packageConfig.setService("service");
        packageConfig.setServiceImpl("service.impl");
        packageConfig.setController("controller");
        autoGenerator.setPackageInfo(packageConfig);
        //策略配置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setInclude("product_info","product_category");
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setEntityLombokModel(true);
        TableFill tableFill = new TableFill("create_time", FieldFill.INSERT);
        TableFill tableFill2 = new TableFill("update_time", FieldFill.INSERT_UPDATE);
        List<TableFill> list = Arrays.asList(tableFill,tableFill2);
        strategyConfig.setTableFillList(list);
        autoGenerator.setStrategy(strategyConfig);
        //启动
        autoGenerator.execute();
    }
}

```

#### 3.注解

+ 主启动类添加扫描注解@MapperScan

#### 4.若要整合mybatis

```yml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  mapper-locations: com/zsy/mapper/xml/*.xml
```

#### 5.分页查询

1. 增加分页配置

   ```java
   @Configuration
   @MapperScan("com.zsy.mapper*")
   public class MybatisPlusConfiguration {
   
       @Bean
       public PaginationInterceptor paginationInterceptor() {
           PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
           // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
           // paginationInterceptor.setOverflow(false);
           // 设置最大单页限制数量，默认 500 条，-1 不受限制
           // paginationInterceptor.setLimit(500);
           // 开启 count 的 join 优化,只针对部分 left join
           paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
           return paginationInterceptor;
       }
   }
   
   ```

2. 封装Page，调用selectPage方法

```java
 Page<OrderMaster> orderMasterPage = new Page<>(page,size);
        Page<OrderMaster> result = orderMasterMapper.selectPage(orderMasterPage,wrapper);
        List<OrderMaster> list = result.getRecords();
```

#### 6.自动填充uuid

```java
 @TableId(value = "order_id", type = IdType.ASSIGN_UUID)
```

**@TableId**

1、如果数据库字段设成user_id
在初始生成后，在代码中会变成userId，不会设置成主键
使用**@TableId(value=“user_id”,type = IdType.AUTO)**注解

“value”：设置数据库字段值
“type”：设置主键类型、如果数据库主键设置了自增建议使用“AUTO”

type有六种类型类型，最下面三个只有插入主键为空时，才会自动填充

| 类型          | 解释                                                     |
| ------------- | -------------------------------------------------------- |
| AUTO          | 数据库自增ID                                             |
| NONE          | 数据库未设置主键类型（将会跟随全局）                     |
| INPUT         | 用户输入ID（该类型可以通过自己注册自动填充插件进行填充） |
| ID_WORKER     | 全局唯一ID (idWorker)                                    |
| UUID          | 全局唯一ID（UUID）                                       |
| ID_WORKER_STR | 字符串全局唯一ID（idWorker 的字符串表示）                |

> 如果主键自增，不插入时，数据库自动填充自增值

#### 7.自动填充时间

需要一个handler

```java
package com.southwind.handler;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
public class MyObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("createTime", LocalDateTime.now(), metaObject);
        this.setFieldValByName("updateTime", LocalDateTime.now(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime", LocalDateTime.now(), metaObject);
    }
}
```