## 一、匿名内部类与lambda

简化这样一个过程：创建类，实现接口，重写方法，new出这个类。

匿名内部类和lambda是将这四部简化为一步 。

一句话：创建了一个实现xx接口的类并new出了这个对象

```java
public class TestThread2 {
    public static void main(String[] args) {
        //1.正常定义类
        ILike like = new Like();
        like.lambda();

        //2.匿名内部类的形式,new接口名字
        like = new ILike() {
            public void lambda() {
                System.out.println("i like lambda2");
            }
        };//创建一个匿名类，实现了ILike接口，并重写了方法
        like.lambda();

        //3.lambda表达式形式
        like = ()->{
            System.out.println("i like lambda3");
        };//创建一个匿名类，实现了ILike接口，并重写了方法
        like.lambda();

    }
}

//定义一个函数式接口
interface  ILike{
    void lambda();
}
class Like implements ILike{
    public void lambda() {
        System.out.println("i like lambda1");
    }
}
```

然后看怎么快速启动一个线程

```java
public class TestThread3 {
    public static void main(String[] args) {
        new Thread(()->System.out.println("lt is a pig")).start();
    }
}
```

## 二、重新理解synchronized

每个对象对应一把锁，每个synchronized方法都必须拿到调用该方法的对象的锁才能执行，否则就阻塞，执行过程中独占锁，执行结束后释放锁。