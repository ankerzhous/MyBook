### 在JSP中编写java代码的三种方式

+ jsp脚本

```jsp
<%
	java
%>
```

+ jsp声明

```
<%!
	java 方法
%>
```

+ jsp表达式

```jsp
<%= java 变量>
```



### JSP九大内置对象作用域

#### 9个内置对象

为什么叫内置对象

1. request:  HttpServletRequest类的对象
2. response： HttpServletResponse类的对象
3. session： HttpSession的对象
4. application：  表示当前的web应用，保存所用用户的共享数据，Servletcontext。
5. pageContext： 页面上下文，获取当前页面信息，PageContex类的对象。
6. config： 获取当前Servlet的对象，ServletConfig类的对象
7. out： 向用户输出信息，JspWriter类的对象
8. page：当前JSP转换后的Servlet, Servlet接口的实现类对象
9. exception： jsp页面发生的异常， Exceptino类的对象 



### 4个内置作用域

page作用域：对应的内置对象是pageContext                                   只在当前页面有效

request作用域：对应的内置对象是request										一次request有效

session作用域：对应的内置对象是 session										一次会话

application作用域：对应的内置对象是application							整个应用上下文



### request的常用方法

1. String getParameter(String name)
2. void setAttribute(String key, Object value)
3. Object getAttribute(String key)
4. RequestDispatcher getRequestDispatcher(String path) 返回一个 RequestDispatcher 对象，该对象表示要跳转的目标资源，通过该对象的 forward 方法完成跳转。

getParameter VS getAttribute

getParameter 获取浏览器（客户端）传给服务器的参数（无论访问的是 JSP 还是 Servlet）。

getAttribute 用于服务器不同组件之间的值传递（Servlet、JSP），调用之前一定要先 setAttribute



### response 的常用方法

sendRedirect(String path) 重定向，资源的跳转

setContentType("") 设置响应的数据格式，一般用作处理中文乱码的问题





## jstl

主要进行逻辑处理，遍历集合

el得作用是取数据 getattribute

1.foreach 

items ：集合

var：集合得每个元素

varStatus：每个元素得属性 .count  .index

```jsp
<c:forEach items="${list}" var="str" begin="2" end ="3" step= "2" varStatus="sta">
	${sta.count},${str}<br/>
</c:forEach>
```







