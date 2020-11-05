## html

1. 一个按钮

```html
<input name = "button" type = "button" onclick="func()" value = "点我" >
```

2. 超链接

```html
<a href="www.baidu.com" target="_self"></a>//在原网页跳转（不写时候的默认）
<a href="www.baidu.com" target="_blank"></a>//打开新网页
```

3. 空格

```html
&nbsp;
```

4. 锚点

```html
<a name="tag">标记位</a>
<a href="#tag"></a>
```

5. 表单

   把表单的数据绑定到请求中 

   每个标签都要有 name，这样才能把前台用户选择的数据传入后端

   value可以是指定后让用户 选择，也可以用户的输入

```html
<form action="www.badidu.com" method="post" enctype="multipart/form-data">
    <input type="file" name="img">
	<input type="radio" name = "gender" value="0">男
    <input type="radio" name = "gender" value="1">女
</form>
```

**input属性(输入框，选择框)**

+ name 用来传值的参数名称

+ type
  + text，password，file，submit
  + reset 重置表单信息的按钮
  + radio 单选框
  + checkbox 复选框
+ value
  + 用户提交的值就是value
  + radio，checkbox，的value只能是用户指定的

**select属性**（下拉列表）

```html
<select name="province">
 <option value="1" >浙江省<option>
 <option value="1">湖南省<option>
 <option value="1">广东省<option>
</select>
```

**textarea（文本域）**

```HTML
<textarea name="desc" rows="12" cols="30"></textarea>
```

