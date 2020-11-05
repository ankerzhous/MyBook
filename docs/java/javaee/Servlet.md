## Servlet概述

### 1.是什么

​	接口，有五大方法

```java
//Tomcat创建Servlet对象，依次调用init service destory 三个周期方法，我们在service中写业务 
public interface Servlet{
    public ServletConfig getServletConfig()
    public String getServletInfo(){ return null }
    
    //以下是三个周期方法
    public void init(ServletConfig servletconfig){}
    //我们的工作是编写这个service业务  
    public void service(ServletRequest request,ServletResponse response){}
    public void destory(){}
    
}
```

### 2.如何创建该类

​		 1.实现javax.servlet.Servlet接口

​		 2.继承 javax.servlet.GenericServlet类

​		 3.继承javax.servlet.http.HttpServlet类

### 3.联合tomcat使用

#### 		1.过程：

​				获得该类后,写service方法，然后tomcat 依次调用这些个方法 init service destory ，其中的service是我们要去写的

#### 		2.给servlet绑定url 

​		在web.xml中进行配置 前一个是起别名 ，后一个是绑定url

​		tomcat 使用servlet，并在web.xml中加载需要的配置信息

​		

<img src="C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200228152950367.png" alt="image-20200228152950367" style="zoom: 80%;" />





## Servletconfig

周期方法init的参数，在servelt初始化方法中构造该对象，每个servlet一个，

作用：通过config对象，可以拿到servlet的初始化参数，context对象

#### ![image-20200228162737520](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200228162737520.png)

## HttpServlet

![image-20200228192849574](C:\Users\Sander\AppData\Roaming\Typora\typora-user-images\image-20200228192849574.png)

405：4打头客户端错误，没有重写表示不支持这种方式

500：5打头服务端错误

302：重定向 3打头中转

2打头成功

## servlet一些细节

### 1.Servlet与线程安全

​	一个类型的servlet只有一个实例对象，那么就有可能一个servlet处理多个请求，所以不是线程安全的，但是效率高

### 2.启动tomcat时创建

​	在web.xml中配置

```xml
<load-on-startup>0</load-on-startup>
```

### 3.url-pattern 

​		1）.该标签是<url-mapping>的子元素，用于指定servlet的访问路径，可以有多个路径

```
<servlet-mapping>
        <servlet-name>XXX</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
```

#### 		2）通配符的使用

​				a.  *在前   ***.jsp**    所有.jsp路径

​				b.  * 在后      **/servlet/.*** 是匹配  /servlet/a          /servlet/b

​				c.   **/***           匹配所有url

## ServletContext

### 1.获取方式

- ​	在 void init(Servletconfig config)中：ServletContext context = config.getServletContext();

-  GenericServlet  类中有gerServletContext()方法,所以可以直接使用 this.getServletContext()

  httoServlet继承自 GenericServle

  

### 2.获取域对象的方式

ServletContext是javaweb四大域对象之一：

- l PageContext；最小的一个，当对JSP的请求时开始，当响应结束时销毁
- l ServletRequest；整个请求链（请求转发也存在）。
- l HttpSession；浏览器，一个用户，为用户服务的
- l ServletContext；一个webapp一个

### 3.作用

​	



## response常用方法

**1.通过response.getWriter(); 获取一个PrintWriter 对象**

**可以使用println(),append(),write(),format()等等方法设置返回给浏览器的html内容。**

**2.response.sendRedirect("fail.html");**客户端跳转

```
PrintWriter pw= response.getWriter();
pw.println("<h1>Hello Servlet</h1>");
```

response.setContentType("text/html");

\1. response.setContentType("text/html; charset=UTF-8");
不仅发送到浏览器的内容会使用UTF-8编码，而且还通知浏览器使用UTF-8编码方式进行显示。所以总能正常显示中文

\2. response.setCharacterEncoding("UTF-8");
仅仅是发送的浏览器的内容是UTF-8编码的，至于浏览器是用哪种编码方式显示不管。 所以当浏览器的显示编码方式不是UTF-8的时候，就会看到乱码，需要手动再进行一次设置。

## request常用方法

**1.request.getParameter()**

**2.服务段跳转 request.getRequestDispatcher("success.html").forward(request, response);**

request.getParameterValues(): 用于获取具有多值的参数，比如注册时候提交的 "hobits"，可以是多选的。
request.getParameterMap(): 用于遍历所有的参数，并返回Map类型。

**3.setAttribute和getAttribute可以用来在进行服务端跳转的时候，在不同的Servlet之间进行数据共享。**

**request.getRequestURL():** 浏览器发出请求时的完整URL，包括协议 主机名 端口(如果有)"
**request.getRequestURI():** 浏览器发出请求的资源名部分，去掉了协议和主机名"
**request.getQueryString():** 请求行中的参数部分，只能显示以get方式发出的参数，post方式的看不到
**request.getRemoteAddr():** 浏览器所处于的客户机的IP地址
**request.getRemoteHost():** 浏览器所处于的客户机的主机名
**request.getRemotePort():** 浏览器所处于的客户机使用的网络端口
**request.getLocalAddr():** 服务器的IP地址
**request.getLocalName():** 服务器的主机名
**request.getMethod():** 得到客户机请求方式一般是GET或者POST



**request.getHeader()** 获取浏览器传递过来的头信息。
比如getHeader("user-agent") 可以获取浏览器的基本资料，这样就能判断是firefox、IE、chrome、或者是safari浏览器
**request.getHeaderNames()** 获取浏览器所有的**头信息名称**，根据头信息名称就能遍历出所有的头信息

## session	对象

1.简介：服务器为浏览器创建的一个会话对象，当用户访问其他程序时，其他程序可以从用户的session中取出为用户服务。

2.如何获取：Session对象由服务器创建，开发人员可以调用request对象的getSession方法得到session对象