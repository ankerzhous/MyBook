## 一、Spring思路与快速配置

Spring是个非侵入框架。

### 1.思路图

![image-20200426141949373](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200426141949373.png)

### 2.配置文件头

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd ">
</beans>
```

### 3.名称空间汇总

需要的时候加上

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:c="http://www.springframework.org/schema/c"
	xmlns:cache="http://www.springframework.org/schema/cache"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xmlns:jee="http://www.springframework.org/schema/jee"
	xmlns:lang="http://www.springframework.org/schema/lang"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:util="http://www.springframework.org/schema/util"
	xsi:schemaLocation="http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.3.xsd
		http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.3.xsd
		http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-4.3.xsd
		http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/lang http://www.springframework.org/schema/lang/spring-lang-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd ">
```

### 4.依赖

```xml
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-context</artifactId>
     <version>5.2.5.RELEASE</version>
</dependency>
```

Bean类型 ：

```java
@Data
public class Person {
     String name;
    Integer age;
    String address;
    Car car;
    List<Book> books;
    Map<String,Object> mp;
    Properties properties;
}
```

## 二、给Bean赋值的方式

### 1.无参构造+set方法注入

```xml
<bean id = "person" class = "com.zsy.Person">
    <property name="name" value="lt"></property>
    <property name = "age" value = "17"></property>
    <property name="address" value="湖南"></property>
</bean>
```

### 2.有参构造

```xml
<--!如果省略name属性，就按找构造器中参数顺序写或者用idex指定参数顺序-->
<bean id="person1" class="com.zsy.Person">
        <constructor-arg name="name" value="zsy"></constructor-arg>
        <constructor-arg name="address" value="sd"></constructor-arg>
        <constructor-arg name="age" value="21"></constructor-arg>
 </bean>
```

### 3.p名称空间

```xml
<bean id = "person" class = "com.zsy.Person"
 p:name="lt" P:agev= "17" p:address = "湖南"
</bean>
```

###  4.工厂模式

比如有个飞机类，获取飞机对象是都是通过工厂的get方法得到的

静态工厂意味着工厂对象不需要创建直接调用静态方法，实例工厂意味着工厂对象要创建。

**静态工厂：**

要获取什么，通过哪个工厂，哪个方法 。

```java
public  class AirplaneStaticFactory {
    public static Airplane getAirplane(String jzName){
        System.out.println("静态工厂正在执行....");
        Airplane airplane =  new Airplane();
        airplane.setFdj("太行");
        airplane.setFjsName("zsy");
        airplane.setJzName(jzName);
        airplane.setPersonNum(100);
        return  airplane;
    }
}
```

```java
<bean id="Airplane1" class="com.zsy.factory.AirplaneStaticFactory" factory-method="getAirplane" >
    <constructor-arg value="jzName"></constructor-arg>
</bean>
```

**实例工厂：**

1.先创建工厂对象

2.再通过工厂bean和工厂方法创建对象

```java
public class AirPlaneInstanceFactory {
    public  Airplane getAirplane(String jzName){
        System.out.println("实例工厂正在执行....");
        Airplane airplane =  new Airplane();
        airplane.setFdj("太行");
        airplane.setFjsName("zsy");
        airplane.setJzName(jzName);
        airplane.setPersonNum(100);
        return  airplane;
    }

}
```

```xml
<bean id="AirplaneInstanceFactory" class="com.zsy.factory.AirPlaneInstanceFactory"></bean>
<bean id="Airplane2" class="com.zsy.bean.Airplane" factory-bean="AirplaneInstanceFactory" factory-method="getAirplane">
    <constructor-arg value="王五"></constructor-arg>
</bean>
```

**实现FactoryBean的工厂**：

实现spring的工厂接口，Spring可以自动识别，所以配置简单。

```java
public class MyFactoryBeanImpl implements FactoryBean {
    public Book getObject() throws Exception {
        Book book = new Book();
        book.setName("杀手");
        return book;
    }

    public Class<?> getObjectType() {
        return Book.class;
    }

    public boolean isSingleton() {
        return true;
    }
}
```

```xml
<bean id="book1" class="com.zsy.factory.MyFactoryBeanImpl"></bean>
```

### 5.注解方式

@component

@Service

@repository

@Controller

```java
@Service
public class BookService{
}
```

## 三、从容器中获取对象

### 1.通过唯一标识符id

```java
person person =(Person)ioc.getBean("person1")
```

### 2.通过类名

1. getBean重载

   从容器中获取并赋值

```java
person person = ioc.getBean(Person.class);
person person = ioc.getBean("person1",Person.class);
```

  2.  ==@Autowired==

      自动装配

```
@Autowired
Person person1;
```

## 四、给属性赋值复杂类型

在property标签里给集合类，其他bean类赋值

给复杂类型赋值时，都在property得子标签中完成

### 1.null

```xml
<bean id="person" class="com.zsy.Person">
        <property name="age">
            <null></null>
        </property>
</bean>
```

### 2.引用类型

引用其他bean、引用内部bean

引用内部bean时在property内部创建<bean>标签

```java
<bean id="car1" class="com.zsy.Car">
    <property name="price" value="121212"></property>
    <property name="color" value="red"></property>
</bean>
<bean id="person" class="com.zsy.Person">
    <property name="age" value="18"></property>
    <property name="car" ref="car1"></property>
</bean>
```

### 3.List

```xml
<bean id="book2" class="com.zsy.Book">
        <property name="name" value="高等数学"></property>
        <property name="price" value="21"></property>
    </bean>
    <bean id="person1" class="com.zsy.Person">
        <property name="books">
            <!--books = new Arraylist<>;-->
            <list>
                <bean id="book1" class="com.zsy.Book">
                    <property name="price" value="11"></property>
                    <property name="name" value="西游"></property>
                </bean>
                <ref bean="book2"/>
            </list>
        </property>
</bean>
```

### 4.properties

```xml
<property name="properties">
    <props>
        <prop key="username">12</prop>
        <prop key="password">admin</prop>
    </props>
</property>
```

### 5.util名称空间

+ 先在头上引入util名称空间

  插入了第4行 第8先插入再注册 

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:util="http://www.springframework.org/schema/util"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/util
         http://www.springframework.org/schema/util/spring-util-4.1.xsd"
  >
  ```

+ 编写的map可以全局引用了

  ```xml
  <bean id ="person4" class="com.zsy.Person">
          <property name="mp" ref="myMap"></property>
      </bean>
  <util:map id="myMap">
          <entry key="key1" value="1"></entry>
          <entry key="key2" value="周沈懿"></entry>
          <entry key="key3" value-ref="book2"></entry>
          <entry key="key4" >
              <bean class="com.zsy.Car">
                  <property name="price" value="11111"></property>
                  <property name="color" value="yellow"></property>
              </bean>
          </entry>
  </util:map>
  ```

### 6.级联属性

先拿到引用，并给引用的属性赋值

会修改原来的bean中的值

```xml
<bean id="car1" class="com.zsy.Car">
    <property name="price" value="121212"></property>
    <property name="color" value="red"></property>
</bean>
<bean id="person06" class="com.zsy.Person">
    <property name="car" ref="car1"></property>
    <property name="car.color" value="1"></property>
</bean>
```

## 五、Bean的高级配置

### 1. Bean标签的其他属性

1. parent 继承

继承另一个bean可以消除重复部分

```xml
<bean id = "person2" class = "com.zsy.Person" parent = "person">
    <property name="name" value="龙藤02"></property>
</bean>
```

2. abstract模板，不能直接实例化，必须通过继承使用

```xml
<bean id = "person2" class = "com.zsy.Person" abstract = "true">
    <property name="name" value="龙藤"></property>
    <property name = "age" value = "17"></property>
    <property name="address" value="湖南"></property>
</bean>
```

3. scope 作用范围

    singleton:单例，容器启动前就创建好并保存在容器中了

    prototype多例，容器启动时不创建，获取的时候创建bean

### 2. Bean的生命周期方法

​		单例：创建（容器创建）------->初始化------>销毁（容器关闭）

​		多例：创建（获取bean）------>初始化------>==容器关闭不会销毁==

​	note：创建指的是已经把数据注入bean了。

​	调用步骤：	

		1. 在bean里写初始化和销毁方法
  		2. 在xml中配置init-method 和 destory-method

```java
public class Book {
    private int price;
    private String name;
    public Book(){
        System.out.println("book创建...");
    }
    public void Myinit(){
        System.out.println("book初始化...");
    }
    public void Mydestory(){
        System.out.println("book销毁....");
    }
}
```

```xml
<bean id = "person2" class = "com.zsy.Person" init-method="" destory-method="">
    <property name="name" value="龙藤"></property>
    <property name = "age" value = "17"></property>
    <property name="address" value="湖南"></property>
</bean>
```

测试：

为了能关闭ioc 必须使用 ApplicationContext的子接口

```java
public class Test2 {
    ConfigurableApplicationContext ioc = new ClassPathXmlApplicationContext("ApplicationContext.xml");
    @Test
    public void test2(){
       System.out.println("容器关闭...");
       ioc.close();
    }
}	  
```

结果：

单例模式：

![image-20200427101915351](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200427101915351.png)

多例模式：

![image-20200427101945350](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200427101945350.png)

### 3. 后置处理器 

bean在==初始化方法==前后可以做一些处理

用法：

1. 编写BeanPostProcessor接口的实现类，该接口在 org.springframework.beans下

2. 在容器中注册

1.实现接口，重写方法

```java
public class MyBeanPostProcessor implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName+"初始化前...."+bean);
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName+"初始化后...."+bean);
        return bean;
    }
}
```

2.xml中注册

```xml
<bean id="beanPostProcessor" class="com.zsy.bean.MyBeanPostProcessor"></bean>
```

### 4. 配置数据库和连接池

配置c3p0连接池和数据库

#### 1).引用内部属性

​	<1>依赖c3p0 和mysql驱动

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
</dependency>
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.2</version>
</dependency>
```

​	<2>xml配置

```xml
 <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSceource">
        <property name="user" value="root"></property>
        <property name="password" value="admin"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3307?useUnicode=true&amp;characterEncoding=UTF-8
   &amp;serverTimezone = GMT&amp;useSSL=false"></property>
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
</bean>
```

​	<3>测试

从容器中获取连接

```java
@Test
public void test2() throws SQLException {
    DataSource dataSource = (DataSource) ioc.getBean("dataSource");
    System.out.println(dataSource.getConnection());
}
```

#### 2).引用外部属性

​	<1>在resources目录下创建dbcongfig.properties

```properties
user = root
password = admin
jdbcUrl = jdbc:mysql://localhost:3307?useUnicode=true & characterEncoding=UTF-8 & serverTimezone = GMT & useSSL=false
driverClass=com.mysql.cj.jdbc.Driver
```

​	<2>需要context名称空间

在汇总里找一个总的名称空间 在写上路径

```xml
<context:property-placeholder location="classpath:dbconfig.properties"></context:property-placeholder>
```

​	<3>${}的方式引用

注意：${username} 是spring特有属性

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="user" value="${user}"></property>
    <property name="password" value="${password}"></property>
    <property name="jdbcUrl" value="${jdbcUrl}"></property>
    <property name="driverClass" value="${driverClass}"></property>
</bean>
```

## 六、SpEl

### 1.基本语法

SpEL使用#{…}作为定界符，所有在大框号中的字符都将被认为是SpEL表达式。

### 2.使用字面量

●整数：<property name="count" value="#{5}"/>

●小数：<property name="frequency" value="#{89.7}"/>

●科学计数法：<property name="capacity" value="#{1e4}"/>

●String类型的字面量可以使用单引号或者双引号作为字符串的定界符号

<property name=“name” value=**"#{'Chuck'}"**/>

<property name='name' value=**'#{"Chuck"}'**/>

●Boolean：<property name="enabled" value="#{false}"/>

### 3.引用其他bean

相当于ref

```xml
  <bean id="emp04"  class="com.atguigu.parent.bean.Employee"*>   
      <property name="empId"  value="1003"/>   
      <property name="empName"  value="Kate"/>   
      <property name="age"  value="21"/>    
      <property name="detp"  value="#{dept}"/> 
</bean>  

 
```

### 4.引用其他bean的属性值作为自己某个属性的值

```xml
 <bean id="emp05"  class="com.atguigu.parent.bean.Employee">   
     <property name="empId" value="1003"/>  
     <property name="empName"  value="Kate"/>  
     <property name="age"  value="21"/>  
     <property name="deptName"  value="#{dept.deptName}"/>  </bean>  

 
```

### 5.调用非静态方法

```xml
  <!-- 创建一个对象，在SpEL表达式中调用这个对象的方法 -->  
<bean id="salaryGenerator"  class="com.atguigu.spel.bean.SalaryGenerator"/>
<bean id="employee"  class="com.atguigu.spel.bean.Employee">  
    <!-- 通过对象方法的返回值为属性赋值 -->    
    <property name="salayOfYear"  value="#{salaryGenerator.getSalaryOfYear(5000)}"/> 
</bean>  
```

### 6.调用静态方法

```xml
<bean id="employee" class="com.atguigu.spel.bean.Employee">   
      <!-- 在SpEL表达式中调用类的静态方法 -->   
      <property name="circle"  value="#{T(java.lang.Math).PI\*20}"/> 
</bean>  
```

### 7.运算符

①算术运算符：+、-、*、/、%、^

②字符串连接：+

③比较运算符：<、>、==、<=、>=、lt、gt、eq、le、ge

④逻辑运算符：and, or, not, |

⑤三目运算符：判断条件?判断结果为true时的取值:判断结果为false时的取值

⑥正则表达式：matches

## 七、注解

### 1.组件标注

​	①普通组件：@Component

​		标识一个受Spring IOC容器管理的组件

​	②持久化层组件：@Respository

​		标识一个受Spring IOC容器管理的持久化层组件

​	③业务逻辑层组件：@Service

​		标识一个受Spring IOC容器管理的业务逻辑层组件

​	④表述层控制器组件：@Controller

​		标识一个受Spring IOC容器管理的表述层控制器组件

​	⑤组件命名规则

​		[1]默认情况：使用组件的简单类名首字母小写后得到的字符串作为bean的id

​		[2]使用组件注解的value属性指定bean的id

举个栗子：

```java
@Service
public class BookService{
}
```

相当于

```xml
<bean id = "bookService" class ="BookService"></bean>
```

### 2.扫描组件

​	①指定被扫描的package

```xml
<context:component-scan base-package="com.zsy"></context:component-scan>
```

​	②详细说明

​		[1]**base-package**属性指定一个需要扫描的基类包，Spring容器将会扫描这个基类包及其子包中的所有类。

​		[2]当需要扫描多个包时可以使用逗号分隔。

​		[3]如果仅希望扫描特定的类而非基包下的所有类，可使用resource-pattern属性过滤特定的类，示例：

```xml
  <context:component-scan     base-package="com.atguigu.component"      resource-pattern="autowire/*.class"/>   
```

​		[4]包含与排除

​			●<context:include-filter>子节点表示要包含的目标类

注意：通常需要与use-default-filters属性配合使用才能够达到“仅包含某些组件”这样的效果。即：通过将use-default-filters属性设置为false，禁用默认过滤器，然后扫描的就只是include-filter中的规则指定的组件了。

​			●<context:exclude-filter>子节点表示要排除在外的目标类

​			●component-scan下可以拥有若干个include-filter和exclude-filter子节点

​			●过滤表达式

| 类别       | 示例                      | 说明                                                         |
| ---------- | ------------------------- | ------------------------------------------------------------ |
| annotation | com.atguigu.XxxAnnotation | 过滤所有标注了XxxAnnotation的类。这个规则根据目标组件是否标注了指定类型的注解进行过滤。 |
| assignable | com.atguigu.BaseXxx       | 过滤所有BaseXxx类的子类。这个规则根据目标组件是否是指定类型的子类的方式进行过滤。 |
| aspectj    | com.atguigu.*Service+     | 所有类名是以Service结束的，或这样的类的子类。这个规则根据AspectJ表达式进行过滤。 |
| regex      | com\.atguigu\.anno\.*     | 所有com.atguigu.anno包下的类。这个规则根据正则表达式匹配到的类名进行过滤。 |
| custom     | com.atguigu.XxxTypeFilter | 使用XxxTypeFilter类通过编码的方式自定义过滤规则。该类必须实现org.springframework.core.type.filter.TypeFilter接口 |

### 3.组件装配

@Autowired 

1. 先按类型装配，有相同则再按按变量名找。

2. 默认请况下找不到会报错，@Autowired(required="false") 找不到就装配null。

3. 在@Autowired前加上注解 @Qualifiter("BookService") 可以指定id装配 。该注解也可以在形参前标。

   ```java
   @Qualifiter("BookService2")
   @Autowired 
   private Bookservice bookservice;
   ```

4. 给方法标注，会给形参装配值。

@Autowired, @Resource, @Inject 区别：

1. @Resource 是java的标准，@Autowired是spring的。
2. @Resource拓展性更强。



## 八、其他细节

1. 接口可以加入容器，但是不会创建对象，只是告知容器可能有这样一个对象。