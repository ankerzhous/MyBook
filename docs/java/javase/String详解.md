### String三大特性

不变性：String底层时char数组，延长会使其指向另一个对象，而修改不会变成另一个对象。

常量池优化：创建一个字符串首先会去常量池中寻找，如果没有则在常量池中创建一个。

final：该类不能被继承。



#### String str = new String("java")

new的方式创建的字符串在堆中，直接创建的字符串在常量池里



#### intern() 方法

当调用某个字符串的intern()时，回去字符串常量池中寻找，如果存在一个值相等的字符串，会直接返回该对象的引用。如果不存在，则在常量池中创建并返回

```java
String str = "java";//在常量池
String str2 = new String("java");//在堆内存
System.out.println(str == str2.intern());//返回true
```



#### string值传递问题

```java

public class StringDemo {
    public static void main(String[] args) {
        String str = "java";//在常量池
        System.out.println(str.hashCode());
        System.out.println(str);
    }
    public static void replace(String str1){
        str1.replace('j','l');
    }
```

请问返回什么？

返回 java

String 的 replace方法返回的是一个新创建的对象，但是也不接收返回值，str1本身就没有改变。

所以str1 = str  指向 "java";