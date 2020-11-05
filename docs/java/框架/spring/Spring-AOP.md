## 一、动态代理

含义：用代理对象执行，增强了原对象的方法。

主要需要反射包下的 Proxy类和InvocationHandler接口。

代理对象和被代理对象必须都实现同一接口

spring01_aop01工程

### 如何使用

1. 创建代理类 CalculatorProxy ，写一个 getPxoxy方法去获得代理对象。

   Proxy类

   使用java.lang.reflect包下的 Proxy类的newProxyInstance方法生成代理对象

   要有原来对象的加载器，实现的接口和方法执行器。

   ```java
   Object proxy = Proxy.newProxyInstance(calculator.getClass().getClassLoader(),calculator.getClass().getInterfaces(),invocationHandler);
   ```

2. 方法执行器改写，进行方法增强

   InvocationHandler接口，

   重写invoke方法，增强调用。

   ```java
   InvocationHandler invocationHandler = new InvocationHandler() {
               public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                   System.out.println("执行了......"+method);
                   Object result = method.invoke(calculator,objects);
                   return result;
               }
   };
   ```

   invoke() 方法 可以动态执行方法，第一个参数是对象，第二个参数是参数

### 代理类

```java
public class CalculatorProxy {
    public static Calculator getPxoxy(final Calculator calculator){
        //方法执行器
        InvocationHandler invocationHandler = new InvocationHandler() {
            /*
            * Method : 将要执行的目标对象的方法
            * objects : 方法的参数
            * */
            public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                 Object result = null;
                try {
                    LogUtils.logStart()；
                    result = method.invoke(calculator, objects);
                    LogUtils.logReturn()；
                } catch (Exception e) {
                    LogUtils.logException()；
                } finally {
                    LogUtils.logEnd()；
                }
              
                return result;
            }
        };

        Object proxy = Proxy.newProxyInstance(calculator.getClass().getClassLoader(),calculator.getClass().getInterfaces(),invocationHandler);
        return (Calculator) proxy;
    }
}
```

### 测试类

```java
public class test {
    @Test
    public void test(){
        Calculator calculator = new CalculatorImpl();
        Calculator Proxy = CalculatorProxy.getPxoxy(calculator);
        System.out.println(Proxy.add(1,2));
    }
}
```

### 日志工具类

```java
public class LogUtils {
    public static void logStart(){
        System.out.println("方法开始执行，参数是");
    }
    public static void logReturn(){
        System.out.println("执行完成，结果是");
    }
    public static void logException() {
        System.out.println("执行异常，异常是");
    }
    public static void logEnd() {
        System.out.println("方法结束");
    }
}
```



## 二、springAOP实现代理

springAop大大简化了动态代理配置并且更加灵活

不用写代理直接写执行前后的方法切入

spring01_aop01工程

### 1.导入包

```xml
 <dependency>
     <groupId>org.aspectj</groupId>
     <artifactId>aspectjrt</artifactId>
     <version>1.6.11</version>
</dependency>

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.6.11</version>
</dependency>

<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.1</version>
</dependency>
```

注意：还需要spring-aop包，但在springcontext中是有的

application.xm中开启自动代理

```xml
 <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

### 2.注解

| 注解            | 作用         |
| --------------- | ------------ |
| @Before         | 方法作用之前 |
| @After          | 方法结束     |
| @AfterReturning | 正常返回之后 |
| @AfterThrowing  | 抛出异常之后 |
| @Around         | 环绕         |

+ 将切面类和目标类注入ioc容器 @Component
+ 并给切面类加@Aspect标识是个切面类
+ 给切面类的方法加上注解和表达式标注在哪个类的哪个方法的执行前or后切入

```java
@Aspect
@Component
public class LogUtils {
    //在Calculator中的所有方法前切入
    @Before("execution(public int com.zsy.Calculator.*(int,int))")
    public static void logStart(){
        System.out.println("方法开始执行，参数是");
    }
    @AfterReturning("execution(public int com.zsy.Calculator.*(int,int))")
    public static void logReturn(){
        System.out.println("执行完成，结果是");
    }
    @AfterThrowing("execution(public int com.zsy.Calculator.*(int,int)) ")
    public static void logException() {
        System.out.println("执行异常，异常是");
    }
    @After("execution(public int com.zsy.Calculator.*(int,int))")
    public static void logEnd() {
        System.out.println("方法结束");
    }
}
```

### 3.细节

1. 容器中保存的是代理对象，因为有切面，把原对象切了。如果去掉界面，拿到的就是对象本身。

2. 接口可以不标注，标注了也不会创建

3. ```java
   Calculator calculator = ioc.getBean(Calculator.class);//必须是Calculator.class接口类型，不能是CalculatorImpl类型。Calculator.class才能被找到，因为代理也实现了这个接口。
   ```

4. 如果删除了接口，由jdk变为cglib为对象创建代理，创建不需要接口的代理

   ```java
   Calculator calculator = ioc.getBean(CalculatorImpl.class);//这时可以通过该对象找到代理
   ```

   

## 三、切入点表达式

1. 固定格式：excution("访问权限符 返回类型 方法全签名")

2. 通配符：

​	*: 

​		1.匹配一个或多个字符，一级目录

​		2.匹配任意一个参数

​		3.一层路径

​		4.权限位置*不行，不写就行

​	..：

​		1.匹配多个参数，任意类型

​		2.任意多层路径

​		最模糊的： * *(..)         *  *****.*(..)  //任意返回类型，任意包下任意方法，任意个任意类型参数 

​		最精确的：execution("public int com.zsy.Calculator.add(int int)")

3. &&   ||   ！ 表达式都满足，有一个满足，不满足  的切

4. 可重用表达式抽取：

   ```java
    @Pointcut("execution(public int com.zsy.Calculator.*(int,int))")
       public void hahaMypoint(){
    }
   ```

5. 加入返回类型，方法名，异常信息

   想要的目标执行的信息，只要在切面方法上标注

   (JoinPoint joinPoint, Object result)  returning = "result"   

   目标方法名称：joinPoint.getSignature().getName() 

   目标方法返回：result

   ```java
   @AfterReturning(value = "execution(public int com.zsy.Calculator.*(int,int))",returning = "result")
       public static void logReturn(JoinPoint joinPoint,Object result){
           System.out.println(joinPoint.getSignature().getName()+"执行完成，结果是"+result);
    }
   
   @AfterThrowing(value = "execution(public int com.zsy.Calculator.*(int,int))",throwing = "exception")
       public static void logException(JoinPoint joinPoint,Exception exception) {
           System.out.println("执行异常，异常是"+exception);
       }
   ```

## 四、环绕通知

进一步简化四个标注，四个切入点写在一个方法里

```java
 public void logAround(ProceedingJoinPoint joinPoint) throws Throwable{
        String name = joinPoint.getSignature().getName();
        System.out.println("环绕前置"+name+"方法开始执行，参数是"+Arrays.asList(joinPoint.getArgs()));
        Object[] args = joinPoint.getArgs();
        try {
           Object proceed =  joinPoint.proceed(args);
            System.out.println("环绕返回" name+"方法返回,返回结果是"+proceed);
        } catch (Exception e) {
            System.out.println("环绕异常"+"执行异常，异常是"+e);
            throw new RuntimeException();
        } finally {
            System.out.println("环绕后置" +name+"方法结束";
        }

    }
```

如果四个切面方法和通知都有：

日志前置--环绕前置--目标方法执行--环绕返回--环绕后置--日志后置--日志返回

多切面时@Order(1) 越小优先级越高，先进后出，环绕只影响一个切面。同心圆模型

![image-20200430111037509](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200430111037509.png)

## 五、事务控制

### 1.基本使用

1. 基本的aop包即可
2. 需要tx名称空间
3. 把事务控制器装入ioc容器，配置数据源

```xml
<bean id="tm" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="pooledDataSource"></property>
</bean>
<tx:annotation-driven transaction-manager="tm"></tx:annotation-driven>
```

4. 给事务方法加注解 @Transactional

### 2.隔离级别

数据库事务并发问题：

多个线程启用多个事务时出现的一些问题，数据库提供隔离级别来解决这些问题

1. 脏读，读取了个了别人修改后未提交的数据。读取了无效的脏数据
2. 不可重复读，两次读的数据不一致。有其他事务在修改
3. 幻读，多读了或者少读了。有其他事务在增删