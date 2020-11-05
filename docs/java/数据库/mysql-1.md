## 数据库

时区问题，MySQL 美国人开发，默认使用的时间是美国时间，中国是东八区，要比美国早 8 个小时，解决方案，手动设置 MySQL 的时间，+8

```sql
set global time_zone='+8:00';
```

Maven 是一个远程仓库，里面装载了各种各样的 Java jar。

## 数据库存储引擎

存储引擎其实就是如何存储数据，如何为存储的数据建立索引，如何更新，查询数据等技术的实现。

关系型数据库中存储引擎也可以称为表类型。

MySQL 提供了多种存储引擎。SQL 语句

SQL 语句是专门用来管理数据库的一种编程语言。各种数据库中使用的 SQL 基本一致，个别地方有细微的差别，数据库方言来解决不同数据库 SQL 冲突。

汉语，普通话，

粤语、陕西话、湖南话...方言

```
show engines;
```

Engine：存储引擎的名称

Support：MySQL 是否支持该引擎

Comment：描述

Transactions：是否支持事务

XA：是否支持分布式事务

Savepoints：保存点，以便事务进行回滚

MySQL 数据库默认的存储引擎是 InnoDB，InnoDB 对事务的处理能力非常强大，是其他存储引擎所不能比拟的。

InnoDB 支持自增（自动增长），自增列不能为空，必须是主键。

InnoDB 存储引擎支持外键。

InnoDB 优势在于提供了良好的事务管理功能，数据崩溃修复能力和并发控制。

InnoDB 读写能力稍差，占用的数据空间相对较大。

## 如何操作数据库

使用 SQL 语句来完成，Java 程序，使用 ORM （Hibernate、MyBatis、Spring Data），底层都是 SQL 语句。

SQL 的分类：

- DML（数据操作语言）操作数据库中的数据（insert、update、delete）
- DDL（数据定义语言）创建、删除、修改数据库、数据表
- DQL（数据查询语言）对数据进行查询
- DCL（数据控制语言）用来控制数据库组件的事务操作（提交 commit、回滚 rollback）

## 数据库的组成

1、表结构：表的设计 //列信息

2、表数据：表中保存的数据 //行信息

## 创建数据库

初学者使用 SQL 语句创建

```sql
create database mydb1 default character set utf8 collate utf8_general_ci;
create database mydb2 default character set utf8 collate utf8_bin;
```

collate 校验字符集是对数据表中存储的非数值数据进行排序的

utf8_general_ci：不区分大小写。a B C D

utf8_bin：区分大小写，比较的是字母的二进制编码

B-》66，a-》97

## 创建数据表

SQL

```sql
create table tablename(
	//字段
    //字段名 数据类型 默认值 是否可以为null 自增 主外键...
    id int,
    name varchar(11)
)
```

整数类型：tinyint(1 个字节)、smallint（2 个字节）、mediumint（3 个字节）、int（4 个字节）、bigint（8 个字节）

浮点型：float（4 个字节）、double（8 个字节）

日期和时间类型：datetime、date、timestamp、time、year

year（1 个字节）范围 1901~2155

time（3 个字节）范围 -838：59：59 - 838：59：59

date（3 个字节）范围 1000-01-01 ~ 9999:12:31

datetime(8 个字节) 范围 1000-01-01 00:00:00 ~ 9999:12:31 23:59:59

timestamp(4 个字节) 表示 1970 年 1 月 1 号 0 点到现在的毫秒数

字符串类型

char(n)、varchar(n)、text(n)

二进制类型

bit、binary、tinyblob、blob、mediumblob、longblob

tinyblob 最多 255 个字节

blob 2的16次方-1 个字节

mediumblob 2的24次方-1 个字节

longblob 2的32次方-1 个字节

## 管理数据库结构

- 删除数据库

```sql
drop database dbname;
```

- 查看数据库

```sql
show databases;
```

- 选择数据库

```sql
use dbname;
```

- 查看所有表名

```sql
show tables;
```

- 查看表结构

```
desc tablename;
```

- 删除数据表

```
drop table dbname;
```

- 修改表结构(alter)

  - 新增一个字段

  ```
  alter table tablename add sex varchar(2);
  ```

  - 修改一个字段(modify和change)

  ```
  alter table tablename change sex gender varchar(1);
  alter table user modify age int not null;
  ```

  - 删除一个字段

  ```
  alter table tablename drop sex;
  ```

## 管理数据

CRUD

- 添加数据

```
insert into tablename(字段列表) values(值)
```

- 修改数据

```
update tablename set 字段名=值,... where id = 1;
```

- 查询数据（查询要现有字段名）

```
select 字段列表 from tablename;
```

- 删除数据(是删一条记录所以最短)

```
delete from tablename where id = 1;
```

## SQL 函数

### 数学函数

abs() 求绝对值

floor() 返回小于参数的最大整数

ceil() 返回大于参数的最小整数

```
select abs(score) from user;
```

### 字符串函数

insert(s1,index,len,s2)，下标从 1 开始

```
select insert(name,2,3,'新的字符串') from user;
```

upper() 字母转大写

lower() 字母转小写

left(s,len) 从左边开始截取 len 长度的数据

right(s,len) 从右边开始截取 len 长度的数据

substring(s,index,len) 从 index 开始截取 len 长度的数据

reverse() 反序输出

### 日期函数

curdate() current_date() 获取系统日期

curtime() current_time() 获取系统时间

now() 获取系统日期+时间

datediff(d1,d2) 计算两个日期之间相差的天数

adddate(d,n) 计算d日期n天之后的日期

subdate(d,n) 计算d日期n天之前的日期



## 聚合函数

统计某个字段的信息：最大值，最小值，平均值，总和

计算某个字段所有值的和 sum(字段)

求某个字段的平均值 avg(字段)

求某个字段的最大值 max(字段)

求某个字段的最小值 min(字段)

```
select count(*) from user;
select sum(money) from user;
```

分组

就是将查询的结果，更具其中一个字段再划分

```
select name,avg(score) from student group by name;
```

排序

```
select name,avg(score) from student group by name order by avg(score) DESC;
```

```
select name,score from student order by score DESC;
```

分组再选择

```
select name,avg(score) from student group by name having avg(score) > 88;
```

```
select name,avg(score) from student group by name having avg(score) > 88 order by avg(score) DESC;
```



## MySQL 运算符

### 算术运算符

```
select score/100 from student;
```

### 比较运算符

```
select score>90 from student;
```

比较运算符返回 0/1，0 表示 false，1 表示 true

### 逻辑运算符 

&&、||、！

```
select !score > 90 || id > 2 from student where id = 1;
```

### 特殊运算符

is null 判断值是否为空

```
select name is null from student where id = 9
select name is not null from student where id = 9
```

between and 判断值是否在某个区间内，两个临界值都可以取到

```
select score between 60 and 88 from student where id = 7;
select score >= 60 && score <= 88 from student where id = 7;
```

in 判断值是否在某个确定值的集合内

```
select score from student where id in (1,2,3);
select score from student where id = 1 || id = 2 || id = 3;
```

like 模糊查询

name 中包含“小”

```
select * from student where name like '%小%';
```

name 以“小”开头

```
select * from student where name like '小%';
```

name 以“小”结尾

```
select * from student where name like '%小';
```

name 长度为 2 

 ```
select * from student where name like '__';
 ```

name 长度为 3，以“小”开头

```
select * from student where name like '小__';
```

name 长度为 3，以“小”结尾

```
select * from student where name like '__小';
```

name 长度为 3，中间是 “小”

```
select * from student where name like '_小_';
```



## 表设计

### 主键

表中的一个字段，给该字段添加主键设置，该字段的值是每一行记录的唯一标识。

默认情况下，每张表都得有一个主键，一张表只能有一个主键，所谓的一张表多个主键，是指联合主键。

主键一般定义为 int 类型，因为 int 类型存储空间较小，int 类型支持自增（自动增加）。

主键值由数据库负责生成，自增的方式。

```sql
create table aa(
    id int primary key auto_increment,
    name varchar(11)
);
```

### 外键

表中的一个字段，给该字段添加外键设置，与其他表的主键建立约束关系（其他表的主键要约束这个外键，外键可以取的值必须是主键的值）

```sql
create table orders(
    id int primary key auto_increment,
    cost int,
    user_id int,
    foreign key (user_id) references user(id)
);
```

外键一般不要跟本表中的主键进行约束。

删除外键

```sql
alter table orders drop foreign key orders_ibfk_1
```

添加外键

```sql
alter table orders add foreign key(user_id) references user(id)
```

实际开发中一般不使用外键，为了提高数据库的效率，关系通过逻辑控制来处理。



## 我的理解

#### 1.orderBy 对查询结果进行排序

#### 2.limit和offset的使用

+ 一个参数：表示取几条数据

+ 两个参数：跳过几个取几个 

```sql
select * from db1 limit 1,4  //
```

表示取第2，3，4，5，条数据

+ 和offset一起使用：取limit个跳过offset个

```sql
select * from db1 limit 4 offset 1;
```

表示取第2，3，4，5，条数据

#### 3.group by 和聚合函数

https://blog.csdn.net/u014717572/article/details/80687042

group by相当于把单元格合并了，聚合就是把合并过后产生的多值转成单一值。

#### 4.having 和 where的区别

where 是在查询前筛选，having是在查询后筛选。

所以where里可以出现查询字段中没有的数据，having则不可以出现。