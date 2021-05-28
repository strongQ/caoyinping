---

layout:        post
title:         sql-summary project
description:   整理sql部分语法
keywords:      sql
categories:    [数据库]
tags:          [sql]

---


### 1、创建更新表

```sql
create table <表名>(
<列名1> <数据类型> <约束条件>
）
alter table <表名> add column <列名> <数据类型>;     //增加新列
alter table <表名> drop column <列名>;               //删除列
rename table <旧表名> to <新表名>;
alter table <旧表名> rename to <新表名>;
drop table <表名>;
```

### 2、子句操作符

```sql
BETWEEN  在某个范围内
LIKE     搜索某种模式
IN       针对某个列多个可能值
%        任意字符
_        一个字符
concat(str,str1,str2...)   拼接字符

case when <表达式1> then <表达式>
when <表达式2> then <表达式>
...
else <表达式>
end
```

### 3、函数处理数据

```sql
left(s, n)          //返回字符串左边的n个字符
right(s, n)         //返回字符串右边的n个字符
trim(s)             //去掉字符串s的空格
ltrim(s)            //去掉字符串左边的空格
rtrim(s)            //去掉字符串右边的空格
length(s)           //计算字符串的长度
lower(s)            //将字符串的所有字符变成小写
upper(s)            //将字符串的所有字符变成大写
abs(x)             //绝对值
cos(x)
sin(x)
tan(x)
acos(x) asin() atan()     //三角函数
ceil(x)             //返回大于或等于x的最小整数
floor(x)            //舍去x的小数部分，只保留整数部分，并且不进行四舍五入
pi()               //返回圆周率
sqrt(x)            //平方根
pow(x, y)          //x的y次方
exp(x)             //返回e的x次方
rand()             //返回0-1的随机数
round(x, y)            //返回x的值小数点后面的第y位，进行四舍五入
truncate(x, y)     //返回x的值小数点后面的第y位，不进行四舍五入

current_date()                  //返回当前日期
current_time()                  //返回当前时间
current_timestamp()/now()             //返回当前日期和时间
date()                          //从日期或日期时间中提取出日期值
year() month() day() hour() minute()
date_format(d, f)               //以指定格式显示日期
datediff(date1,date2)           //返回两个日期相差的天数，大日期在前
timediff(datetime1,datetime2)   //返回形式为时:分:秒，大日期在前
timestampdiff(interval,time1,time2)     //返回两个日期之差，大日期在后。精确到什么单位取决于第一个参      数  interval，可以为年---秒
```

### 4、分组数据

```sql
avg()
min()
max()
count()
sum()

select <列名> from <表名>
where <过滤条件>
group by <列名>
having <过滤条件>       组级过滤
order by <列名> <排序方式>
// group by 多列，多列值相同分组
```

### 5、插入数据

```sql
insert into <表名>(列名1,列名2,...) values (数据...);
insert into <表名>(列名表)
select <列名表> from <表名>;  //插入检索的数据

create table copytable as
select * from <表名>;        //复制表
```

### 6、视图

```sql
create view <视图名> as
<select语句>;
drop view <视图名>;
```

### 7、基础定义

```sql
DDL：数据定义语言，用来创建、删除存储数据的数据库以及数据库中的表对象，包含的指令有：CREATE、DROP、ALTER
DML：数据操纵语言，用来查询或者变更表中的记录。包含的指令有：SELECT、INSERT、UPDATE、DELETE
DCL：数据控制语言，用来确认或者取消对数据 库中的数据进行的变更。包含的指令有：COMMIT、ROLLBACK、GRANT、REVOKE
```

### 8、例题

```sql
已知表格：Product(maker, type, model)

Get the makers who produce only one product type and more than one model. Output: maker, type

select distinct maker, type

from Product

where maker in

(select maker from Product

group by maker

having count(distinct type)=1 and count(distinct model)>1);
```

### 9、sql性能优化

```sql
1.尽可能在数据库设计时，不要使用NULL，尽可能使用NOT NULL；
2.尽可能使用VARCHAR/NVARCHAR 代替CHAR/NCHAR；
3.尽可能使用数字型字段，若只含数值信息的字段尽量不要设计为字符型；
4.为经常需要排序、分组、查询和联合操作的字段建立索引
```

### 10、添加索引

```sql
1. 添加PRIMARY KEY（主键索引）
ALTER TABLE `table_name` ADD PRIMARY `column` )
2. 添加UNIQUE(唯一索引)
ALTER TABLE `table_name` ADD UNIQUE ( `column` ) 
3. 添加CLUSTERED聚集索引(表中只会有一个）
ALTER TABLE `table_name` ADD CLUSTERED(`column`) 
4. 添加FULLTEXT(全文索引)
ALTER TABLE `table_name` ADD FULLTEXT ( `column`) 
5. 添加多列索引
ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3` )
6. 采用OR、<>或!=操作符时，查询语句不会使用索引；
7. 采用NOT操作符时，查询语句不会使用索引；
8. 采用HAVING操作符时，查询语句不会使用索引；
```

### 11、Cross Apply 和 Outer Apply用法

相当于inner join 和 left join

```sql
SELECT * FROM tableA a CROSS APPLY tableB b

WHERE a.id=b.id

// 相同

SELECT * FROM tableA a CROSS APPLY (select * from tableB where id=a.id) b

// 语文第一名，数学前两名，英语前三名

SELECT b.* FROM (
select Subject='Chiness',num=1 union all
select 'Math',2 union all
select 'English',3
)a cross apply (select top(a.num) * from Students where Subject=a.Subject )b

```



### 12、显示执行计划

```sql
// 显示分析、编译、和执行毫秒数
SET STATISTICS TIME ON
```