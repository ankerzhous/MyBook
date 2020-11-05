## 文件对象

一个字母占一个字节，一个汉字三个字节（utf-8）

### 1.创建文件

```java
package file;
  
import java.io.File;
  
public class TestFile {
  
    public static void main(String[] args) {
        // 绝对路径
        File f1 = new File("d:/LOLFolder");
        System.out.println("f1的绝对路径：" + f1.getAbsolutePath());
        // 相对路径,相对于工作目录，如果在eclipse中，就是项目目录
        File f2 = new File("LOL.exe");
        System.out.println("f2的绝对路径：" + f2.getAbsolutePath());
  
        // 把f1作为父目录创建文件对象
        File f3 = new File(f1, "LOL.exe");
  
        System.out.println("f3的绝对路径：" + f3.getAbsolutePath());
    }
}
```

### 2.文件常用方法1

```java
package file;
  
import java.io.File;
import java.util.Date;
  
public class TestFile {
  
    public static void main(String[] args) {
        
        File f = new File("d:/LOLFolder/LOL.exe");
        System.out.println("当前文件是：" +f);
        //文件是否存在
        System.out.println("判断是否存在："+f.exists());
         
        //是否是文件夹
        System.out.println("判断是否是文件夹："+f.isDirectory());
          
        //是否是文件（非文件夹）
        System.out.println("判断是否是文件："+f.isFile());
          
        //文件长度
        System.out.println("获取文件的长度："+f.length());
          
        //文件最后修改时间
        long time = f.lastModified();
        Date d = new Date(time);
        System.out.println("获取文件的最后修改时间："+d);
        //设置文件修改时间为1970.1.1 08:00:00
        f.setLastModified(0);
          
        //文件重命名
        File f2 =new File("d:/LOLFolder/DOTA.exe");
        f.renameTo(f2);
        System.out.println("把LOL.exe改名成了DOTA.exe");
         
        System.out.println("注意： 需要在D:\\LOLFolder确实存在一个LOL.exe,\r\n才可以看到对应的文件长度、修改时间等信息");
    }
}
```

### 3.文件常用方法2

```java
package file;
  
import java.io.File;
import java.io.IOException;
  
public class TestFile {
  
    public static void main(String[] args) throws IOException {
  
        File f = new File("d:/LOLFolder/skin/garen.ski");
  
        // 以字符串数组的形式，返回当前文件夹下的所有文件（不包含子文件及子文件夹）
        f.list();
  
        // 以文件数组的形式，返回当前文件夹下的所有文件（不包含子文件及子文件夹）
        File[]fs= f.listFiles();
  
        // 以字符串形式返回获取所在文件夹
        f.getParent();
  
        // 以文件形式返回获取所在文件夹
        f.getParentFile();
        // 创建文件夹，如果父文件夹skin不存在，创建就无效
        f.mkdir();
  
        // 创建文件夹，如果父文件夹skin不存在，就会创建父文件夹
        f.mkdirs();
  
        // 创建一个空文件,如果父文件夹skin不存在，就会抛出异常
        f.createNewFile();
        // 所以创建一个空文件之前，通常都会创建父目录
        f.getParentFile().mkdirs();
  
        // 列出所有的盘符c: d: e: 等等
        f.listRoots();
  
        // 刪除文件
        f.delete();
  
        // JVM结束的时候，刪除文件，常用于临时文件的删除
        f.deleteOnExit();
  
    }
}
```

## 流

流可以实现不同存储介质之间的数据交换

### 1.字节输入流

FileInputStream FileOutputStream

```java
			File f =new File("d:/lol.txt");
            //创建基于文件的输入流
            FileInputStream fis =new FileInputStream(f);
            //创建字节数组，其长度就是文件的长度
            byte[] all =new byte[(int) f.length()];
            //以字节流的形式读取文件所有内容
            fis.read(all);
            for (byte b : all) {
                //打印出来是65 66
                System.out.println(b);
            }
             
            //每次使用完流，都应该进行关闭
            fis.close();
```

### 2.字符输入流

FileReader  FileWriter

```java
            File f = new File("d:/lol.txt");
            // 创建基于文件的Reader
            try (FileReader fr = new FileReader(f)) {
                // 创建字符数组，其长度就是文件的长度
                char[] all = new char[(int) f.length()];
                // 以字符流的形式读取文件所有内容
                fr.read(all);
                for (char b : all) {
                    // 打印出来是A B
                    System.out.println(b);
            }
```

### 3.缓存流

缓存流在读取的时候，**会一次性读较多的数据到缓存中**，以后每一次的读取，都是在缓存中访问，直到缓存中的数据读取完毕，再到硬盘中读取。

FileReader  PrintWriter

```java
 	//1.缓存字符输入流
		File f = new File("d:/lol.txt");
        // 创建文件字符流
        // 缓存流必须建立在一个存在的流的基础上
        try (
                FileReader fr = new FileReader(f);
                BufferedReader br = new BufferedReader(fr);
            )
        {
            while (true) {
                // 一次读一行
                String line = br.readLine();
                if (null == line)
                    break;
                System.out.println(line);
            } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
            
```

```java
		2.// 缓存字符输出流， 可以一次写出一行数据
		File f = new File("d:/lol2.txt");
          
        try (
                // 创建文件字符流
                FileWriter fw = new FileWriter(f);
                // 缓存流必须建立在一个存在的流的基础上              
                PrintWriter pw = new PrintWriter(fw);              
        ) {
            pw.println("garen kill teemo");
            pw.println("teemo revive after 1 minutes");
            pw.println("teemo try to garen, but killed again");
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
```

### 4.PrintWriter 

常用方法 println() 输出一行内容

而HttpServletResponse 有个 getwrite()方法 产生一个PrintWriter的输出流

```java
response.getwriter().println("lt");
```

