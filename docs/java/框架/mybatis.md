## 一、原生形式（不使用接口）

### 1.目录结构

![image-20200407122821378](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200407122821378.png) 

### 2.依赖导入

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.5</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
</dependency>
//lombok可选
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>
```

### 3.配置文件

#### 1)mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--1.起别名，自动扫描src中 com.zsy.pojo下的类型，使得在后续配置文件Category.xml中使用resultType的时候，可以直接使用Category,而不必写全com.how2java.pojo.Category-->
    <typeAliases>
        <package name="com.zsy.pojo"/>
    </typeAliases>

    <!--2.提供连接数据库用的驱动，数据库名称，编码方式，账号密码-->
    <environments default="development">
        <environment id="development">
            <!-- 配置JDBC事务管理 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- POOLED配置JDBC数据源连接池 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"></property>
                <property name="driver" value="com.mysql.cj.jdbc.Driver"></property>
                <property name="url" value="jdbc:mysql://localhost:3306/how2java?useUnicode=true&amp;characterEncoding=UTF-8"></property>
                <property name="username" value="root"></property>
                <property name="password" value="admin"></property>
            </dataSource>
        </environment>
    </environments>

    <!--导入映射关系 第2个配置的位置-->
    <mappers>
        <mapper resource="mappers/Category.xml"/>
    </mappers>
</configuration>
```

#### 2).Category.xml

​	位置：实体类中，本例中是com.how2java.pojo目录

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- mapper:根标签，namespace：命名空间，随便写，一般保证命名空间唯一 -->
<mapper namespace="mymapper">
    <!-- statement，内容：sql语句。id：唯一标识，随便写，在同一个命名空间下保持唯一
       resultType：sql语句查询结果集的封装类型,category_为数据库中的表
     -->
    <select id="findAll" resultType="Category">
      select * from category_
   </select>
</mapper>

```

### 4.测试类Test1

```java
import com.zsy.pojo.Category;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class Test1 {
    public static void main(String[] args) throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        try {
            List<Category> cs = sqlSession.selectList("findAll");
            for(Category c:cs){
                System.out.println(c.getName());
            }
        } finally {
            sqlSession.close();
        }

    }
}

```

### 5.总结

MyBatis使用步骤总结

1. 配置mybatis-config.xml 全局的配置文件 (1、数据源，2、外部的mapper)
2. 配置mapper
3. 创建工厂，调用sqlsession

## 二、接口动态代理模式

### 1.编写接口

com.zsy.repository.CategoryRepository下写接口

```java
public interface CategoryRepository {
    public List<Category> findAll();
}
```

### 2.注册接口

在全局配置mybatis-config.xml中的命名空间把上述接口的位置注册进去，关键是namespace不能乱写了只能是接口名。

```xml
<mapper namespace="com.zsy.repository.CategoryRepository">
     <select id = "findById" resultType="Category">
        select * from category_ 
    </select>	
</mapper>
```

### 3.测试

```java
import com.zsy.pojo.Category;
import com.zsy.repository.CategoryRepository;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class Test1 {
    public static void main(String[] args) throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //1.拿到接口
        CategoryRepository categoryRepository = sqlSession.getMapper(CategoryRepository.class);
        try {
            //修改select的方式,用接口方法完成查找
            List<Category> cs = categoryRepository.findAll();
            for(Category c:cs){
                System.out.println(c.getName());
            }
        } finally {
            sqlSession.close();
        }

    }
}

```

## 三、mapper细节

### 1）单个参数

#{}中的内容无所谓，一定是接口中传入的参数

```java
public List<Category> findAll(Integer id);
```

```xml
<select id = "findById" resultType="Category">
   select * from category_ where id = #{hhh}
</select>
```

### 2）多个参数

  1.参数必须是 arg0、arg1，否则无法传入

```xml
<select id = "findByIdAndName" resultType="Category">
        select * from category_ where id = #{arg0} and name = #{arg1}
</select>
```

2. 自定义参数名，在映射器接口方法的参数前加注解@Param("")

```java
public Category findByIdAndName(@Param("id") Integer id, @Param("name") String name);
```

```xml
<select id = "findByIdAndName" resultType="Category">
        select * from category_ where id = #{id} and name = #{name}
</select>
```

### 3)  javabean

对象作为参数时，可直接取属性

```java
 public Category findByCategory(Category c);
```

```xml
<select id ="findByCategory" resultType="Category">
        select * from category_ where id =#{id} and name = #{name}
</select>
```

## 四、完整的crud操作

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- mapper:根标签，namespace：命名空间，用于注册接口-->
<mapper namespace="com.zsy.repository.CategoryRepository">
    <select id="findAll" resultType="Category">
      select * from category_
    </select>
    <select id = "findById" resultType="Category">
        select * from category_ where id = #{id}
    </select>
    <select id ="findByName" resultType="Category">
        select * from category_ where name = #{name}
    </select>
    <select id = "findByIdAndName" resultType="Category">
        select * from category_ where id =#{id} and name = #{name}
    </select>
    <select id ="findByCategory" resultType="Category">
        select * from category_ where id =#{id} and name = #{name}
    </select>
    <insert id = "save" >
        insert into category_ (id,name) values (#{id},#{name})
    </insert>
    <update id="update">
         update category_ set name = #{name} where id = #{id}
     </update>
    <delete id="deleteById">
         delete from category_ where id = #{hhh}
    </delete>
</mapper>

```

```java
package com.zsy.repository;

import com.sun.org.glassfish.gmbal.ParameterNames;
import com.zsy.pojo.Category;
import org.apache.ibatis.annotations.Param;

import java.util.List;

public interface CategoryRepository {
    public List<Category> findAll();
    public Category findById(Integer id);
    public Category findByName(String name);
    public Category findByIdAndName(@Param("id") Integer id, @Param("name") String name);
    public Category findByCategory(Category c);
    public void save(Category c);
    public void deleteById(Integer id);
    public void update(Category c);
}
```

## 五、级联查询

先分析sql语句，再设置resultmap对结果集进行封装 ,再在查询时调用resultMap

### 1.一对多

Category

```java
@Data
public class Category {
    private int id;
    private String name;
    private List<Product> products;
}

```

Product

```java
@Data
public class Product {
    private int id;
    private String name;
    private float price;
    private Category category;
}

```

 ProductRepository

```java
public interface ProductRepository {
    public Product findById(Integer id);
}
```

ProductRepository.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- mapper:根标签，namespace：命名空间，用于注册接口-->
<mapper namespace="com.zsy.repository.ProductRepository">
   <resultMap id="productMap" type="com.zsy.pojo.Product">
       <id property="id" column="id"></id>
       <result property="name" column="name"></result>
       <result property="price" column="price"></result>
       <association property="category" javaType="com.zsy.pojo.Category">
           <id property="id" column="cid"></id>
           <result property="name" column="cname"></result>
       </association>
   </resultMap>

    <select id = "findById" resultType="Product" resultMap="productMap">
        select p.id,p.name,p.price,p.cid as cid,c.name as cname from category_ c ,product_ p where p.cid = c.id and p.id = #{id}
    </select>
</mapper>
```

CategotyRepository.xml

关键部位 是 collection,ofType 返回结果用集合接收

```xml
 <resultMap id="CategoryMap" type="com.zsy.pojo.Category">
        <id property="id" column="cid"></id>
        <result property="name" column="cname"></result>
        <collection property="products" ofType="Product">
            <id property="id" column="id"></id>
            <result property="name" column="name"></result>
        </collection>
 </resultMap>
 <select id = "findById" resultType="Category" resultMap="CategoryMap">
        select p.id,p.name,p.price,p.cid as cid,c.name as cname from category_ c ,product_ p where p.cid = c.id and c.id = 1
 </select>
```



### 2.多对多

