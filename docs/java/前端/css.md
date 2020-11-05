## CSS

层叠样式表，定义了如何显示html元素

### 引入样式

1. 行内样式

   ```html
   <div style="width: 300px;height:
   300px;background-color: coral;"></div>
   ```

2. 内部样式，在head里

   ```html
   <!DOCTYPE html>
   <html>
    <head>
    <meta charset="utf-8">
    <title></title>
    <style type="text/css">
    #test {
    width: 300px;
    height: 300px;
    background-color: coral;
    }
    </style>
    </head>
    <body>
    <div id="test"></div>
   </body>
   </html>
        
   ```

3. 外部样式，单独一个文件夹

   用link标签引入本地外部样式

   ```html
   <!DOCTYPE html>
   <html>
    <head>
    <meta charset="utf-8">
    <title></title>
    <link rel="stylesheet"
   href="css/index.css" />
    </head>
    <body>
    <div id="test"></div>
    </body>
   </html>
   ```

### 基本语法

 	选择器+声明

#### 基本选择器

1. 标签选择器

   ```css
   <h1>Hello World</h1>
   h1 {
    color: #FF7F50;
   }
   ```

2. id选择器

   ```css
   <h1 id="text">Hello World</h1>
   #text {
    color: #green;
   }
   ```

3. 类选择器

   ```css
   <h1 class="title">Hello World</h1>
   .title {
    color: #FF7F50;
   }
   ```

### 复合选择器

+ 并集选择器：多个选择器之间⽤ , 分隔，同时选中多个标签 ( ,)

```css
<h1>Hello</h1>
<h2>World</h2>
h1,h2 {
color: red;
}
```

交集选择器：多个选择器直接连接( . )

```css
<h1 class="test1">Hello</h1>

<h1 class="test2">World</h1>

h1.test1 {

color: red;

}
```

后代选择器：多个选择器⽤空格间隔 ( )

```css
<div id="con">
<h1>CSS</h1>
</div>

#con h1{
color: red;
}
```





