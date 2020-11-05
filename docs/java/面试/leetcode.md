## 数字转字符串

### 1.数字转字符

```java
//String类的静态类方法
int i=5;
String str = String.valueof(i);

//封装成对象，调用对象的toString()方法
//内置对象基本都有该方法
Interger it = i;
String str2 = it.toString();


```

### 2.字符转数字

```java
//Integer类的静态类方法
String str = "999";
int i = Integer.parseInt(str);
```



## 字符串

### 1. == equals copareTo

```java
// 1. ==  equals  copareTo
        String s1 = "Hello World";
        System.out.println("s1 is \"" + s1 + "\"");
        String s2 = s1;
        System.out.println("s2 is another reference to s1.");
        String s3 = new String(s1);
        System.out.println("s3 is a copy of s1.");
        // compare using '=='
        System.out.println("Compared by '==':");
        // true since string is immutable and s1 is binded to "Hello World"
        System.out.println("s1 and \"Hello World\": " + (s1 == "Hello World"));
        // true since s1 and s2 is the reference of the same object
        System.out.println("s1 and s2: " + (s1 == s2));
        // false since s3 is refered to another new object
        System.out.println("s1 and s3: " + (s1 == s3));
        // compare using 'equals'
        System.out.println("Compared by 'equals':");
        System.out.println("s1 and \"Hello World\": " + s1.equals("Hello World"));
        System.out.println("s1 and s2: " + s1.equals(s2));
        System.out.println("s1 and s3: " + s1.equals(s3));
        // compare using 'compareTo'
        System.out.println("Compared by 'compareTo':");
        System.out.println("s1 and \"Hello World\": " + (s1.compareTo("Hello World") == 0));
        System.out.println("s1 and s2: " + (s1.compareTo(s2) == 0));
        System.out.println("s1 and s3: " + (s1.compareTo(s3) == 0));
        System.out.println("-------------------------------------------------------	");
```

### 2.字符串内容不可更改  

```java
//新创建
String s = "Hello World";
char[] str = s.toCharArray();
str[5] = ',';
System.out.println(str);
System.out.println("-------------------------------------------------------	");

```

### 3.添加，查找，截取

```java
        String s5 = "Hello World";
        // 1). concatenate
        s1 += "!";
        System.out.println(s5);
  		 // 2). find
        System.out.println("The position of first 'o' is: " + s1.indexOf('o'));
        System.out.println("The position of last 'o' is: " + s1.lastIndexOf('o'));
        // 3). get substring
        System.out.println(s5.substring(6, 11));
        System.out.println(s5);
        System.out.println("-------------------------------------------------------	");
```

### 4.像数组一样取值-charAt

找出字符串中指定位置的值

```
String s = "zsylt";
System.out.println(s.charAt(1));
```



## StringBuffer

可变长字符串的常用方法

### 1.追加，删除，插入，反转

append();        delete();       insert();       reverse();

```java
String s = "Hello lt";
StringBuffer sb =  new StringBuffer(s);
sb.append("be light");
sb.delete(4,10);
sb.insert(4,"there");
sb.reverse();
```



