## 一、基本语法

1. 简介：解释性动态语言，面向对象编程和函数式编程混合。

2. 组成：基础语法 ，核心库， 浏览器操作库，dom操作库，其他api。

3. 变量提升：先解析代码，获取所有声明的变量，再一行一行执行。

```javascript
console.log("a");
var a  = 1;
```

4. 标签的使用，用于跳出双重循环或者代码块。

   ```javascript
   top:
   for (var i = 0; i < 3; i++){
       for (var j = 0; j < 3; j++){
           if (i === 1 && j === 1) break top;
           console.log('i=' + i + ', j=' + j);
       }
   }
   ```

   

## 二、数据类型

1.数据类型

​	有三类共6个：

+ 原始类型： 数字，字符串，布尔

+ 特殊类型： null表示孔， undefine表示未定义

+ 合成类型：对象，对象有三种子类型

  + 狭义的对象即 object

  + 函数 : function 

  + 数组 :Array

    函数也可以看作一种类型赋值给变量，给函数式编程提供便利

2.typeof 判断数据类型

typeof 无法区分数组和对象      instanceof可以

```javascript
typeof ["da"]//object 
[] instanceof Array // true
```

## 三、与网页的交互

1. document.write("")

2. alert()

3. confirm(); 弹出确认框，根据用户选择返回相应结果

4. prompt(str1,str2)  弹出一个消息对话框，返回输入的数据。str1:提示的信息不可更改

5. ```
   window.open([URL], [窗口名称], [参数字符串])
   ```

## 四、DOM

Dom树（document object model）：

1. 文档 ： document ------整个页面
2. 元素 ： element----------每个标签
3. 节点：  node---------------所有内容 标签，属性，内容

都能作为对象使用

