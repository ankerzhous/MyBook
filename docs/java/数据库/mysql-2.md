# 多表关联查询

**基本定义：**

　　left join （左连接）：返回包括左表中的所有记录和右表中连接字段相等的记录。

　　right join （右连接）：返回包括右表中的所有记录和左表中连接字段相等的记录。

　　inner join （等值连接或者叫内连接）：只返回两个表中连接字段相等的行。

　　full join （全外连接）：返回左右表中所有的记录和左右表中连接字段相等的记录。

举个例子：

```
A表　　　　　　　　　　
　id　  name　　
　　1　　小王
　　2　　小李
　　3　　小刘
B表
　　id　　A_id　　job
　　1　　2　　　　老师
　　2　　4　　　　程序员
```

内连接：（只有2张表匹配的行才能显示）

```
select a.name,b.job from A a  inner join B b on a.id=b.A_id


只能得到一条记录
小李　　老师
```

左连接：（左边的表不加限制）

```
select a.name,b.job from A a  left join B b on a.id=b.A_id


三条记录
小王　　null
小李　　老师
小刘　　null
```

右连接：（右边的表不加限制）

```
select a.name,b.job from A a  right join B b on a.id=b.A_id


两条记录
小李　　老师
null　　程序员
```

 

全外连接：(左右2张表都不加限制）

```
select a.name,b.job from A a  full join B b on a.id=b.A_id

四条数据
小王　　null
小李　　老师
小刘　　null
null　　程序员
```

**注：在sql中l外连接包括左连接（left join ）和右连接（right join），全外连接（full join），等值连接（inner join）又叫内连接。**



# 嵌套查询

将多条 SQL 语句进行组合查询，两条 SQL 的嵌套查询，就是将第一条 SQL 的查询结果作为第二条 SQL 的查询条件去继续查询。

1、找出“张三”所有的订单信息。

```sql
select * from orders where user_id = (select id from user where name="张三")
```



# 多对多查询

```sql
select * from course c,student s,student_course sc
where c.id = sc.course_id and s.id = sc.student_id
and s.name = "张三"
```



去重：distinct，去掉重复项

```sql
select distinct c.name,c.id from course c,student s,student_course sc
where c.id = sc.course_id and s.id = sc.student_id
and s.name = "张三"
```

如何判断数据是重复的？

每一列的值都要进行比对，所有列全部相等的情况下，才认为是重复项，否则不是重复项。



分页：limit index,len

index 和 len 都是数值类型，index 表示开始截取的下标，len 表示截取的长度

```sql
select * from mydb1.student limit 30,10;
```



# 索引

索引是提高数据库查询性能的重要方式，跟主外键类似，是添加到字段的一种标识，所有字段都可以添加索引。

索引包括：普通索引，唯一性索引，全文索引，单列索引，多列索引，空间索引。

普通索引：不需要任何限制条件的索引，可以在任意数据类型上添加。

唯一索引：索引的值必须唯一，比如主键索引（主键自带索引）。

全文索引：只能创建在 char、varchar、text 类型的字段上，查询数据量较大的字符串类型的字段时，使用全文索引可以提高查询效率，InnoDB 不支持全文索引。

单列索引：只对应一个字段的索引。

多列索引：在一张表的多个字段上创建索引。

空间索引：只能建立在空间数据类型上（GIS），InnoDB 不支持空间索引。

虽然索引可以提升检索数据的速度，但是创建和维护索引需要耗费资源和时间，索引需要占用物理空间。所以不要给每个字段都添加索引，这样反而有可能降低查询速度。

添加索引的原则：出现在 where 关键字后面的字段添加索引，而不是出现在 select 后面的字段。

1、添加索引

```
alter table user add index in_name(name);
create index in_name on user(name);
```

2、删除索引

```
alter table user drop index in_name;
drop index in_name on user;
```



# 事务

将一个业务逻辑所包含的所有 SQL 语句看作一个整体，要么全部执行成功，要么全部执行失败，不能出现一部分成功，一部分失败的情况。

张三借给李四 500 块钱

2000

张三 500

李四 1000

update set money = 500 where 张三

update set money = 1500 where 李四

4 个特性：

1、原子性：多条 SQL 语句是一个整体，不可再分割。

2、一致性：SQL 语句执行前后，数据库数据的值保持一致。

3、隔离性：一个事务的执行不能被其他事务干扰，张三借钱给李四，不影响张三借钱给王五。

4、持久性：一个事务一旦提交，数据库中数据的改变是永久性的。

事务的操作：

1、开启事务

start transcation;

2、回滚

rollback;

3、提交

commit;



# 视图

视图其实就是一张虚拟的数据表，允许不同用户或应用程序以不同的方式查看同一张表中的数据。

创建视图

```sql
create view view_common as select id,name,age,gender,img from user;
create view view_all as select * from user;
```

使用视图

```sql
select * from view_common;
select * from view_all;
```

删除视图

```sql
drop view view_common;
drop view view_all;
```



# 触发器

触发器中定义一系列的操作，可以在对指定表进行插入、删除、更新操作的同时自动执行这些操作。

优点：

1、开发速度更快。

2、更容易维护，访问目标表会自动调用触发器。

3、业务全局实现，如果需要修改业务，只需要修改触发器，而非业务代码。

创建触发器

```sql
create trigger t_afterinsert_on_tab1
    after insert on tab1
    for each row
    begin
        insert into tab2(tab2_id) values(new.tab1_id);
    end;
```

```sql
create trigger t_afterdelete_on_tab1
    after delete on tab1
    for each row
    begin
        delete from tab2 where tab2_id = old.tab1_id;
    end;
```

删除触发器

```sql
drop trigger t_afterinsert_on_tab1;
drop trigger t_afterdelete_on_tab1;
```



# 存储过程

存储过程是一组为了完成特定功能的 SQL 语句集合，经编译之后存储在数据库中，用户可以直接通过名字调用。

优点：

1、模块化程序设计，只需要创建一次存储过程，以后就可以在程序中调用任意次。

2、执行速度更快，如果某操作需要执行大量的 SQL ，存储过程比 SQL 更快。

3、更好的安全机制，对于没有权限执行存储过程的用户，可以授予权限来操作。

基本形式：

```
create procedure pname(parameters...)
functionbody
```

parameters... 三部分组成：输入输出类型，参数名，参数数据类型

入参存储过程

```sql
create procedure add_name(in target int)
begin
    declare val varchar(20);
    if target = 1 then
       set val = "Java";
    else
       set val = "MySQL";
    end if;
    insert into user(name) values(val);
end;
```

调用存储过程

```sql
call add_name(1);
```

删除存储过程

```sql
drop procedure add_name;
```

执行 SQL 两步：

1、执行 SQL 拿到结果。

2、将结果提交 commit。

一般使用 MySQL 的时候，不需要提交，只需要执行 SQL 即可，因为 MySQL 默认自动提交的。

```
//查看当前 MySQL 自动提交状态
show variables like 'autocommit';
//修改自动提交
set autocommit = 0/1;
```

一旦关闭自动提交之后，执行 SQL 就必须手动进行提交。

```sql
insert into user(name) values('commit');
commit;
```

# 存储过程

存储过程名称+参数列表+方法体

输入/输出类型、参数名、参数数据类型

in/out

出参存储过程：

```sql
create procedure count_of_student(out count_num int)
begin
    select count(*) into count_num from student;
end;
```

调用

```sql
call count_of_student(@count_num);
select @count_num;
```



## 流程控制

```sql
create procedure example_if(in x int)
begin
    if x = 1 then
        select id from student;
    elseif x = 2 then
        select name from student;
    end if;
end;
```

```sql
create procedure example_case(in x int)
begin
    case x
        when 1 then
        select id from student;
        when 2 then
        select name from student;
        end case;
end;
```

```sql
create procedure example_while(out sum int)
begin
    declare i int default 1;
    declare s int default 0;
    while i<=100 do
        set s = s+i;
        set i = i+1;
        end while;
    set sum = s;
end;
```
