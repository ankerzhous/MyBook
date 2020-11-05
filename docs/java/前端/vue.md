### 指令

#### v-text

替换文本内容

```html
 <h2 v-text="message"></h2>
```

#### v-html

和v-text相似，会将内容解析成html文本

#### 1. v-if 

v-if 无参数

test表达式true或false来决定 该标签是否显示 

```html
<p v-if="test"></p> 
```

#### 2.v-bind

v-bind :class = "actice" 等价于  :class  = "active"

绑定样式 ：给class绑定

绑定url ： 给href绑定

```vue
<div id = "app"
<p v-bind:class="{active：isActive}"></p>//active属性 是否绑定取决于isActive得内容
<a v-bind:href="url"></a>//在下面给url一个网址
><div>
<script>
var vm = new vue({
	el:#app
    data:{
    	isActive: true,
    	url:"www.baidu.com"
    }
})
</script>
<style>
    .active{blackground:red;}
</style>
```

#### 3.v-for

可以迭代对象，或者数组

```html
<!-- 实现if 在各种取值显示不同段文字 -->
<!-- for 的使用 -->
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title></title>
<script src="vue.js" type="text/javascript" charset="utf-8"></script>
</head>
<body>
<div id = "app">
	<div v-if="awesome=='lt'">好看</div>
	<div v-else-if="awesome=='zhu'">Oh 丑</div>
	<div v-else-if="awesome=='zsy'">oh 好帅</div>
	<div v-else>hhhh</div>
</div>
<ul id = "example1">
		<li v-for="(value,name,index) in object" >//和顺序有关和名字无关：这三个遍历对象是固定的
			{{index}}---{{name}}--{{value}}
		</li>
</ul>

<script>
	var vm = new Vue({
		el:"#app",
		data:{
			awesome:"ss",
		}
	});
	var example1 = new Vue({
		el:"#example1",
		data:{
			object: {
			      title: 'How to do lists in Vue',
			      author: 'Jane Doe',
			      publishedAt: '2016-04-10'
			    }
		}	
	});
</script>

<style>
</style>


</body>
</html>
```

#### 4.v-on

需要一个参数。

html元素有的事件，就可以绑定,。

触发后然后进入一个函数。

v-on：click = "doit" 等价于 @click = "doit"

还可以绑定 v-on-dblclick双击触发



```html
<html>
<head>
	<meta charset="utf-8">
	<title></title>
	<script src="vue.js" type="text/javascript" charset="utf-8"></script>
</head>
<body>
<div id = "app">
	<button v-on:click="counter+=1"> {{counter}}</button>
	<button v-on:click="counter1('abc')">hhh</button>
</div>
<script>
	var vm = new Vue({
		el:"#app",
		data:{
			counter:1
			
		},
		methods:{
			counter1:function(str){
				alert(str);
			}
		}
	})			
</script>
</body>
</html>
```

#### 5.v-model

给表单和javascript对象做双向数据绑定

v-model="inputForm.dId" 时将和 inputForm.dId 和   option的value双向 绑定 

value又取决dept.deptId的单向绑定

```html
<select class="form-control" v-model="inputForm.dId" name = "dId">
    <option v-for="dept in depts" v-bind:value="dept.deptId" >{{dept.deptName}}</option>
</select>
```

#### 6.页面跳转

$router 时router文件夹，router文件夹下配置了/info的路由

push是跳转 ，跳转到/info ，

```vue
this.$router.push('/info')
```

