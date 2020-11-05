### JDBC 开发

1、加载驱动

2、配置各种参数 url、Driver class、root、password

3、获取 Connection

4、定义 SQL

5、使用 Statement 进行查询

6、ResultSet 需要手动解析

7、释放资源

### MyBatis 使用

1、创建 Maven 工程

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.5</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.21</version>
    </dependency>
    
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.12</version>
    </dependency>
</dependencies>
```

lombok 插件，可以帮助我们生成实体类中的 getter、setter、toString、构造...

IDEA 中使用 Lombok 需要安装插件

2、创建实体类

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class Account {
    private Integer id;
    private String username;
    private String password;
    private Integer age;
}
```

3、创建配置文件，名字可以自定义。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 配置数据源 -->
    <environments default="development">
        <environment id="development">
            <!-- 事务管理 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- 数据源 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybd2"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

4、开发，Mapper 代理实现自定义接口

使用 MyBatis 框架，开发者只需要定义接口即可，不需要自己实现，由 MyBatis 通过 Mapper 代理机制自动帮助我们创建接口的实现类，并且创建接口的实例化对象。

```java
package com.southwind.mapper;

import com.southwind.entity.Account;

import java.util.List;

public interface AccountMapper {
    public List<Account> findAll();
    public Account findById(Integer id);
    public void save(Account account);
    public void update(Account account);
    public void deleteById(Integer id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.AccountMapper">
    <!-- statement -->
    <select id="findAll" resultType="com.southwind.entity.Account">
        select * from account
    </select>

    <select id="findById" parameterType="java.lang.Integer" resultType="com.southwind.entity.Account">
        select * from account where id = #{id}
    </select>

    <insert id="save" parameterType="com.southwind.entity.Account">
        insert into account(username,password,age) values(#{username},#{password},#{age})
    </insert>

    <update id="update" parameterType="com.southwind.entity.Account">
        update account set username = #{username},password = #{password},age = #{age} where id = #{id}
    </update>

    <delete id="deleteById" parameterType="java.lang.Integer">
        delete from account where id = #{id}
    </delete>
</mapper>
```

5、在 config.xml 中注册 Mapper.xml

```xml
<mappers>
    <mapper resource="com/southwind/mapper/AccountMapper.xml"></mapper>
</mappers>
```

6、使用

```java
package com.southwind.test;

import com.southwind.entity.Account;
import com.southwind.mapper.AccountMapper;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

public class Test {
    public static void main(String[] args) {
        //加载 MyBatis 配置文件
        InputStream inputStream = Test.class.getClassLoader().getResourceAsStream("mybatis-config.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //操作API
        AccountMapper mapper = sqlSession.getMapper(AccountMapper.class);
        mapper.findAll().forEach(System.out::println);
    }
}
```

### 常见错误

- Mapper.xml 找不到，pom.xml 配置让 Maven 可以读取 java 路径下 XML 文件。

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

- Mapper.xml 中的 namespace 必须和接口的全类名保持一致。

- Mapper.xml 中的 statement 的 id 值必须和接口方法一致。
- Mapper.xml 必须在 mybatis-config.xml 中进行注册。

### 打印 SQL

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING" />
</settings>
```

# Mapper.xml 常用配置

mapper 是配置文件的根节点，namespace 需要和对应的接口进行关联。因为 MyBatis 会自动读取所有的接口和对应的 Mapper.xml，来动态创建实现类，进而实例化对象，这就是 Mapper 代理机制。

statement 标签一共有 4 种：insert、update、select、delete

id 对应接口的方法名

parameterType 设置方法参数的数据类型映射，支持基本数据类型、包装类、String、多个参数、POJO（对象）。

resultType 设置方法返回值的数据类型映射，支持基本数据类型、包装类、String、POJO（对象）

## 多表关联查询

### 一对多

![image-20200724204748741](C:\Users\ningn\AppData\Roaming\Typora\typora-user-images\image-20200724204748741.png)

![image-20200724204758177](C:\Users\ningn\AppData\Roaming\Typora\typora-user-images\image-20200724204758177.png)

Account --》Orders

```xml
<resultMap id="accountMap" type="Account">
    <id property="id" column="aid"></id>
    <result property="username" column="username"></result>
    <result property="password" column="password"></result>
    <result property="age" column="age"></result>
    <collection property="orders" ofType="Orders">
        <id property="id" column="oid"></id>
        <result property="cost" column="cost"></result>
    </collection>
</resultMap>

<select id="findById" parameterType="java.lang.Integer" resultMap="accountMap">
    select a.id aid,username,password,age,o.id oid,cost from account a,orders o where a.id = o.account_id and a.id = #{id}
</select>
```

Orders --> Account

```xml
<resultMap id="ordersMap" type="Orders">
    <id property="id" column="oid"></id>
    <result property="cost" column="cost"></result>
    <association property="account" javaType="Account">
        <id property="id" column="aid"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <result property="age" column="age"></result>
    </association>
</resultMap>

<select id="findById" parameterType="java.lang.Integer" resultMap="ordersMap">
    select o.id oid,cost,a.id aid,username,password,age from account a,orders o where a.id = o.account_id and o.id = #{id}
</select>
```

```xml
<resultMap id="ordersMap2" type="Orders">
    <id property="id" column="id"></id>
    <result property="cost" column="cost"></result>
    <association property="account" column="account_id" javaType="Account" select="com.southwind.mapper.AccountMapper.findById2"></association>
</resultMap>

<select id="findById2" parameterType="java.lang.Integer" resultMap="ordersMap2">
    select * from orders where id = #{id}
</select>
```

```xml
<select id="findById2" parameterType="int" resultType="Account">
    select * from account where id = #{id}
</select>
```

### 多对多

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.CourseMapper">

    <resultMap id="courseMap" type="Course">
        <id property="id" column="cid"></id>
        <result property="name" column="cname"></result>
        <collection property="students" ofType="Student">
            <id property="id" column="sid"></id>
            <result property="name" column="sname"></result>
        </collection>
    </resultMap>

    <select id="findById" parameterType="java.lang.Integer" resultMap="courseMap">
        select c.id cid,c.name cname,s.id sid,s.name sname from course c,student s,student_course sc where c.id = sc.course_id and s.id = sc.student_id and c.id = #{id}
    </select>
</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.StudentMapper">

    <resultMap id="studentMap" type="Student">
        <id property="id" column="sid"></id>
        <result property="name" column="sname"></result>
        <collection property="courses" ofType="Course">
            <id property="id" column="cid"></id>
            <result property="name" column="cname"></result>
        </collection>
    </resultMap>

    <select id="findById" parameterType="java.lang.Integer" resultMap="studentMap">
        select c.id cid,c.name cname,s.id sid,s.name sname from course c,student s,student_course sc where c.id = sc.course_id and s.id = sc.student_id and s.id = #{id}
    </select>

</mapper>
```

# MyBatis 延迟加载

延迟加载或者叫懒加载、惰性加载、按需加载

提高程序运行效率的一种优化方案，通过延迟加载可以减少 Java 程序与数据库的交互次数。

查询 Orders，获取 Orders 的 Id，此时只查询 Orders 完全可以满足需求，无需查询 Account。

当获取 Orders 级联的 Account 对象，再去查询 Account 即可。

如何开启延迟加载？

1、SQL 语句的分离，不要写一次 SQL 实现两张表的关联查询，这样是无法进行延迟。

2、config.xml 开启延迟加载配置即可。

```xml
<settings>
    <!-- 开启延迟加载 -->
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

