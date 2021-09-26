# 数据库系统原理 第四章

## 第四章 SQL与关系数据库基本操作

**学习重点：**

使用SQL语言实现数据定义、数据更新和**数据查询**三类。

| 数据定义 | 数据更新 | 数据查询 |
| -------- | -------- | -------- |
| CREATE   | INSERT   | SELECT   |
| ALTER    | UPDATE   |          |
| DROP     | DELETE   |          |

数据库基本操作，具体包括数据库模式定义、表定义、视图定义、索引定义、插入数据、删除数据，修改数据、SELECT语句及相关各类子句等。

**学习难点**：

数据查询中各种表连接的方式（交叉连接，内连接，外连接）、GROUP BY子句的使用方法（必须跟聚合出现，跟聚合函数配合）、HAVING子句的使用方法、ORDER BY子句的使用方法和LIMIT子句的使用方法，以及视图定义与各种使用方法。

### 第一节 SQL概述

### 第二节 MYSQL预备知识

###  第三节 数据定义

数据定义语句：创建 - CREATE，删除 - DROP，修改 - ALTER

#### 一，数据库模式定义

创建数据库：create database test;

选择数据库：use test;

修改数据库相关参数（不能修改数据库名）：

```
mysql> alter database test 
    -> default character set gb2312
    -> default collate gb2312_chinese_ci;
Query OK, 1 row affected (0.11 sec)

mysql> show create database test;
+----------+----------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                    |
+----------+----------------------------------------------------------------------------------------------------+
| test     | CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET gb2312 */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+----------------------------------------------------------------------------------------------------+
1 row in set (0.06 sec)
```

删除数据库：drop database test;  [IF EXISTS]

查看数据库：show {databases|schemas} [like 'pattern' |where expr]

#### 二，表定义

创建表：` create table student (Sno char(11) not null,Sname char(10) not null,Sage int(10)); `

查看表：show tables;

查看表结构：
```
show columns in student;

show columns from student;

desc student;
```
更新表：

```
alter table student add column Sbirthday datetime;

alter table student change column Sname Sname char(200);

alter table student drop Sname;
```


重命名表：alter table student rename mystudent;

删除表：drop table mystudent;

#### 三，索引定义

创建索引：
```
create index index_sage on student(Sage);

create table course (id int not null auto_increment,cname char(50) not null,primary key(id),index(cname));

alter table student add index index_Sno(Sno);
```
索引查看：show index in student;

索引删除 ：
```
drop index index_Sno on student;

alter table student drop index index_Sage;
```
### 第四节 数据更新

#### 一，插入数据

```
mysql> desc student;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| Sno   | char(11) | NO   |     | NULL    |       |
| Sname | char(10) | NO   |     | NULL    |       |
| Sage  | int(10)  | YES  |     | NULL    |       |
+-------+----------+------+-----+---------+-------+
3 rows in set (0.05 sec)

插入单行数据：
insert into student(Sno,Sname,Sage) values ('001','lisi','21');

insert student set Sno='002',Sname='zhangsan',Sage='22';

插入多行数据：
insert into student(Sno,Sname,Sage) values ('001','lisi','21'),('002','wangwu','38');
```

查询所有行：select * from student;

```
mysql> select * from student;
+-----+-----------+------+
| Sno | Sname     | Sage |
+-----+-----------+------+
| 001 | lisi      |   21 |
| 002 | zhangsan  |   22 |
| 003 | zhangsan2 |   22 |
| 004 | zhangsan3 |   22 |
+-----+-----------+------+
4 rows in set (0.05 sec)

```

删除数据1：delete from student where Sage=22;

```
mysql> select * from student;
+-----+-------+------+
| Sno | Sname | Sage |
+-----+-------+------+
| 001 | lisi  |   21 |
+-----+-------+------+
1 row in set (0.04 sec)

```

删除数据2：select * from course;  (AUTO_INCREMENT计数器不重置，逐条删除。)

``` 
mysql> select * from course;
+----+-------+
| id | cname |
+----+-------+
|  1 | a1    |
|  2 | a2    |
|  3 | a3    |
+----+-------+
3 rows in set (0.04 sec)

mysql> delete from course;
Query OK, 3 rows affected (0.08 sec)

mysql> insert course set cname='b1';
Query OK, 1 row affected (0.08 sec)

mysql> insert course set cname='b2';
Query OK, 1 row affected (0.10 sec)

mysql> insert course set cname='b3';
Query OK, 1 row affected (0.07 sec)

mysql> select * from course;
+----+-------+
| id | cname |
+----+-------+
|  4 | b1    |
|  5 | b2    |
|  6 | b3    |
+----+-------+
3 rows in set (0.04 sec)
```

删除数据3：truncate course; (AUTO_INCREMENT计数器重置，删除整个表，重新建一个表，效率高于DELETE)

```
mysql> truncate course;
Query OK, 0 rows affected (0.66 sec)

mysql> select * from course;
Empty set

mysql> insert course set cname='c1';
Query OK, 1 row affected (0.09 sec)

mysql> select * from course;
+----+-------+
| id | cname |
+----+-------+
|  1 | c1    |
+----+-------+
1 row in set (0.04 sec)
```

修改数据：update student set Sage=19 where Sno='001';

```
mysql> select * from student;
+-----+--------+------+
| Sno | Sname  | Sage |
+-----+--------+------+
| 001 | lisi   |   21 |
| 002 | wangwu |   18 |
+-----+--------+------+
2 rows in set (0.05 sec)

mysql> update student set Sage=19 where Sno='001';
Query OK, 1 row affected (0.15 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from student;
+-----+--------+------+
| Sno | Sname  | Sage |
+-----+--------+------+
| 001 | lisi   |   19 |
| 002 | wangwu |   18 |
+-----+--------+------+
2 rows in set (0.05 sec)
```

### 第五节 数据查询  **

#### 一，select 语句

#### 二，列的选择与指定

1，查询指定列

select Sage from student;

```
mysql> desc student;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| Sno   | char(11) | NO   |     | NULL    |       |
| Sname | char(10) | NO   |     | NULL    |       |
| Sage  | int(10)  | YES  |     | NULL    |       |
+-------+----------+------+-----+---------+-------+
3 rows in set (0.09 sec)

mysql> select Sage from student;
+------+
| Sage |
+------+
|   19 |
|   18 |
+------+
2 rows in set (0.05 sec)

```

2，查询所有列

select * from student;

3，定义并使用列的别名

select sno as 学号,sname as 姓名,sage as 年龄 from student;

```
mysql> select sno as 学号,sname as 姓名,sage as 年龄 from student;
+------+--------+------+
| 学号 | 姓名   | 年龄 |
+------+--------+------+
| 001  | lisi   |   19 |
| 002  | wangwu |   18 |
+------+--------+------+
2 rows in set (0.07 sec)
```

4，计算列值

select sage+2 from student;

```
mysql> select sage+2 from student;
+--------+
| sage+2 |
+--------+
|     21 |
|     20 |
+--------+
2 rows in set (0.06 sec)
```

新建rec表：

```
mysql> create table rec(id int not null auto_increment,primary key(id),Gno varchar(11),Pno varchar(11),price float,num int not null);
Query OK, 0 rows affected (0.58 sec)

mysql> show tables;
+-----------------+
| Tables_in_shuai |
+-----------------+
| course          |
| rec             |
| student         |
+-----------------+
3 rows in set (0.05 sec)

mysql> desc rec;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| Gno   | varchar(11) | YES  |     | NULL    |                |
| Pno   | varchar(11) | YES  |     | NULL    |                |
| price | float       | YES  |     | NULL    |                |
| num   | int(11)     | NO   |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
5 rows in set (0.05 sec)
```

rec插入数据：

```
mysql> insert into rec(Gno,Pno,price,num) values ('001','001','1.2',300)
    -> ;
Query OK, 1 row affected (0.09 sec)

mysql> insert into rec(Gno,pno,price,num) values('001','002','1.5',200);
Query OK, 1 row affected (0.07 sec)

mysql> insert into rec(Gno,pno,price,num) values('002','001','1.2',170);
Query OK, 1 row affected (0.05 sec)

mysql> insert into rec(Gno,pno,price,num) values('002','002','1.5',130);
Query OK, 1 row affected (0.06 sec)

mysql> insert into rec(Gno,pno,price,num) values('003','001','1.2',110);
Query OK, 1 row affected (0.04 sec)

mysql> insert into rec(Gno,pno,price,num) values('003','002','1.5',60);
Query OK, 1 row affected (0.10 sec)

mysql> desc rec;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| Gno   | varchar(11) | YES  |     | NULL    |                |
| Pno   | varchar(11) | YES  |     | NULL    |                |
| price | float       | YES  |     | NULL    |                |
| num   | int(11)     | NO   |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
5 rows in set (0.05 sec)

mysql> select * from rec;
+----+-----+-----+-------+-----+
| id | Gno | Pno | price | num |
+----+-----+-----+-------+-----+
|  1 | 001 | 001 |   1.2 | 300 |
|  2 | 001 | 002 |   1.5 | 200 |
|  3 | 002 | 001 |   1.2 | 170 |
|  4 | 002 | 002 |   1.5 | 130 |
|  5 | 003 | 001 |   1.2 | 110 |
|  6 | 003 | 002 |   1.5 |  60 |
+----+-----+-----+-------+-----+
6 rows in set (0.05 sec)
```



```
mysql> select price*num as 销售额,gno,pno from rec;
+--------------------+-----+-----+
| 销售额             | gno | pno |
+--------------------+-----+-----+
| 360.00001430511475 | 001 | 001 |
|                300 | 001 | 002 |
|  204.0000081062317 | 002 | 001 |
|                195 | 002 | 002 |
| 132.00000524520874 | 003 | 001 |
|                 90 | 003 | 002 |
+--------------------+-----+-----+
6 rows in set (0.06 sec)
```

5，聚合函数

count()，求组中项数，返回INT类型整数

```
mysql> select count(*) from rec where num>150;
+----------+
| count(*) |
+----------+
|        3 |
+----------+
1 row in set (0.05 sec)
```

max()，求最大值

min()，求最小值 

sum()，求和

avg()，求平均

```
mysql> select max(num*price),gno from rec;
+--------------------+-----+
| max(num*price)     | gno |
+--------------------+-----+
| 360.00001430511475 | 001 |
+--------------------+-----+
1 row in set (0.07 sec)

mysql> select min(num*price),gno from rec;

mysql> select sum(num*price) as 总销售额 from rec;

mysql> select avg(num*price) as 求平均 from rec;
```

#### 三，FROM子句与多表链接查询

新建两个表user，product

```
mysql> desc user;
+-------+----------+------+-----+---------+----------------+
| Field | Type     | Null | Key | Default | Extra          |
+-------+----------+------+-----+---------+----------------+
| id    | int(11)  | NO   | PRI | NULL    | auto_increment |
| uno   | char(10) | NO   |     | NULL    |                |
| uname | char(20) | NO   |     | NULL    |                |
+-------+----------+------+-----+---------+----------------+
3 rows in set (0.07 sec)

mysql> desc product;
+-------+----------+------+-----+---------+----------------+
| Field | Type     | Null | Key | Default | Extra          |
+-------+----------+------+-----+---------+----------------+
| id    | int(11)  | NO   | PRI | NULL    | auto_increment |
| pno   | char(10) | NO   |     | NULL    |                |
| pname | char(20) | NO   |     | NULL    |                |
+-------+----------+------+-----+---------+----------------+
3 rows in set (0.06 sec)

mysql> create table user(id int not null auto_increment,uno char(10) not null,uname char(20) not null,primary key(id));
Query OK, 0 rows affected (0.30 sec)
mysql> create table product(id int not null auto_increment,pno char(10) not null,pname char(20) not null,primary key(id));
Query OK, 0 rows affected (0.25 sec)

mysql> insert user(uno,uname) values('001','张三');
Query OK, 1 row affected (0.07 sec)
mysql> insert user set uno='002',uname='李四';
Query OK, 1 row affected (0.09 sec)
mysql> insert user set uno='003',uname='王五';
Query OK, 1 row affected (0.04 sec)

mysql> insert product set pno='001',pname='产品1';
Query OK, 1 row affected (0.05 sec)
mysql> insert product set pno='002',pname='产品2';
Query OK, 1 row affected (0.07 sec)
mysql> insert product set pno='003',pname='产品3';
Query OK, 1 row affected (0.07 sec)
```



1，交叉连接，又称笛卡尔积。最常使用：` mysql> select * from user,rec where user.uno=rec.gno;  `

```
mysql> select * from user,rec;
+----+-----+-------+----+-----+-----+-------+-----+
| id | uno | uname | id | Gno | Pno | price | num |
+----+-----+-------+----+-----+-----+-------+-----+
|  1 | 001 | 张三  |  1 | 001 | 001 |   1.2 | 300 |
|  2 | 002 | 李四  |  1 | 001 | 001 |   1.2 | 300 |
|  3 | 003 | 王五  |  1 | 001 | 001 |   1.2 | 300 |
|  1 | 001 | 张三  |  2 | 001 | 002 |   1.5 | 200 |
|  2 | 002 | 李四  |  2 | 001 | 002 |   1.5 | 200 |
|  3 | 003 | 王五  |  2 | 001 | 002 |   1.5 | 200 |
|  1 | 001 | 张三  |  3 | 002 | 001 |   1.2 | 170 |
|  2 | 002 | 李四  |  3 | 002 | 001 |   1.2 | 170 |
|  3 | 003 | 王五  |  3 | 002 | 001 |   1.2 | 170 |
|  1 | 001 | 张三  |  4 | 002 | 002 |   1.5 | 130 |
|  2 | 002 | 李四  |  4 | 002 | 002 |   1.5 | 130 |
|  3 | 003 | 王五  |  4 | 002 | 002 |   1.5 | 130 |
|  1 | 001 | 张三  |  5 | 003 | 001 |   1.2 | 110 |
|  2 | 002 | 李四  |  5 | 003 | 001 |   1.2 | 110 |
|  3 | 003 | 王五  |  5 | 003 | 001 |   1.2 | 110 |
|  1 | 001 | 张三  |  6 | 003 | 002 |   1.5 |  60 |
|  2 | 002 | 李四  |  6 | 003 | 002 |   1.5 |  60 |
|  3 | 003 | 王五  |  6 | 003 | 002 |   1.5 |  60 |
+----+-----+-------+----+-----+-----+-------+-----+
18 rows in set (0.09 sec)
```
2，内连接

```
mysql> select * from user join rec on user.uno=rec.gno;
+----+-----+-------+----+-----+-----+-------+-----+
| id | uno | uname | id | Gno | Pno | price | num |
+----+-----+-------+----+-----+-----+-------+-----+
|  1 | 001 | 张三  |  1 | 001 | 001 |   1.2 | 300 |
|  1 | 001 | 张三  |  2 | 001 | 002 |   1.5 | 200 |
|  2 | 002 | 李四  |  3 | 002 | 001 |   1.2 | 170 |
|  2 | 002 | 李四  |  4 | 002 | 002 |   1.5 | 130 |
|  3 | 003 | 王五  |  5 | 003 | 001 |   1.2 | 110 |
|  3 | 003 | 王五  |  6 | 003 | 002 |   1.5 |  60 |
+----+-----+-------+----+-----+-----+-------+-----+
6 rows in set (0.08 sec)

mysql> select * from user,rec where user.uno=rec.gno;
+----+-----+-------+----+-----+-----+-------+-----+
| id | uno | uname | id | Gno | Pno | price | num |
+----+-----+-------+----+-----+-----+-------+-----+
|  1 | 001 | 张三  |  1 | 001 | 001 |   1.2 | 300 |
|  1 | 001 | 张三  |  2 | 001 | 002 |   1.5 | 200 |
|  2 | 002 | 李四  |  3 | 002 | 001 |   1.2 | 170 |
|  2 | 002 | 李四  |  4 | 002 | 002 |   1.5 | 130 |
|  3 | 003 | 王五  |  5 | 003 | 001 |   1.2 | 110 |
|  3 | 003 | 王五  |  6 | 003 | 002 |   1.5 |  60 |
+----+-----+-------+----+-----+-----+-------+-----+
6 rows in set (0.08 sec)
```

3，外连接

左外连接

```
mysql> select * from user left join rec on user.uno=rec.gno;
+----+-----+-------+----+-----+-----+-------+-----+
| id | uno | uname | id | Gno | Pno | price | num |
+----+-----+-------+----+-----+-----+-------+-----+
|  1 | 001 | 张三  |  1 | 001 | 001 |   1.2 | 300 |
|  1 | 001 | 张三  |  2 | 001 | 002 |   1.5 | 200 |
|  2 | 002 | 李四  |  3 | 002 | 001 |   1.2 | 170 |
|  2 | 002 | 李四  |  4 | 002 | 002 |   1.5 | 130 |
|  3 | 003 | 王五  |  5 | 003 | 001 |   1.2 | 110 |
|  3 | 003 | 王五  |  6 | 003 | 002 |   1.5 |  60 |
+----+-----+-------+----+-----+-----+-------+-----+
6 rows in set (0.07 sec)

mysql> insert rec set gno='004',pno='001',price='1.2',num=600; 
Query OK, 1 row affected (0.09 sec)

mysql> select * from user left join rec on user.uno=rec.gno;
+----+-----+-------+----+-----+-----+-------+-----+
| id | uno | uname | id | Gno | Pno | price | num |
+----+-----+-------+----+-----+-----+-------+-----+
|  1 | 001 | 张三  |  1 | 001 | 001 |   1.2 | 300 |
|  1 | 001 | 张三  |  2 | 001 | 002 |   1.5 | 200 |
|  2 | 002 | 李四  |  3 | 002 | 001 |   1.2 | 170 |
|  2 | 002 | 李四  |  4 | 002 | 002 |   1.5 | 130 |
|  3 | 003 | 王五  |  5 | 003 | 001 |   1.2 | 110 |
|  3 | 003 | 王五  |  6 | 003 | 002 |   1.5 |  60 |
+----+-----+-------+----+-----+-----+-------+-----+
6 rows in set (0.07 sec)

```



右外连接

```
mysql> select * from user right join rec on user.uno=rec.gno;
+------+------+-------+----+-----+-----+-------+-----+
| id   | uno  | uname | id | Gno | Pno | price | num |
+------+------+-------+----+-----+-----+-------+-----+
|    1 | 001  | 张三  |  1 | 001 | 001 |   1.2 | 300 |
|    1 | 001  | 张三  |  2 | 001 | 002 |   1.5 | 200 |
|    2 | 002  | 李四  |  3 | 002 | 001 |   1.2 | 170 |
|    2 | 002  | 李四  |  4 | 002 | 002 |   1.5 | 130 |
|    3 | 003  | 王五  |  5 | 003 | 001 |   1.2 | 110 |
|    3 | 003  | 王五  |  6 | 003 | 002 |   1.5 |  60 |
| NULL | NULL | NULL  |  7 | 004 | 001 |   1.2 | 600 |
+------+------+-------+----+-----+-----+-------+-----+
7 rows in set (0.07 sec)

```

#### 四，WHERE 子句与条件查询

##### 1，比较运算符

```
mysql> select * from rec where num>200;
+----+-----+-----+-------+-----+
| id | Gno | Pno | price | num |
+----+-----+-----+-------+-----+
|  1 | 001 | 001 |   1.2 | 300 |
|  7 | 004 | 001 |   1.2 | 600 |
+----+-----+-----+-------+-----+
2 rows in set (0.09 sec)
```



##### 2，确定范围 BETWEEN，NOT BETWEEN

​	查找2到9之间的数：

​	num>=2 and num<=9

​	num between  2 and 9 （注意：小的在前，大的在后）

​	not between不在范围内，在范围外

```
mysql> select * from rec where num between 150 and 300;
+----+-----+-----+-------+-----+
| id | Gno | Pno | price | num |
+----+-----+-----+-------+-----+
|  1 | 001 | 001 |   1.2 | 300 |
|  2 | 001 | 002 |   1.5 | 200 |
|  3 | 002 | 001 |   1.2 | 170 |
+----+-----+-----+-------+-----+
3 rows in set (0.08 sec)
```

##### 3，确定集合 IN，NOT IN

```
mysql> insert product set pno='004',pname='产品4';
Query OK, 1 row affected (0.08 sec)

mysql> select * from product;
+----+-----+-------+
| id | pno | pname |
+----+-----+-------+
|  1 | 001 | 产品1 |
|  2 | 002 | 产品2 |
|  3 | 003 | 产品3 |
|  4 | 004 | 产品4 |
+----+-----+-------+
4 rows in set (0.05 sec)
```

查询哪些产品已销售出 (注：distinct 是唯一，去重)

```
mysql> select * from rec;
+----+-----+-----+-------+-----+
| id | Gno | Pno | price | num |
+----+-----+-----+-------+-----+
|  1 | 001 | 001 |   1.2 | 300 |
|  2 | 001 | 002 |   1.5 | 200 |
|  3 | 002 | 001 |   1.2 | 170 |
|  4 | 002 | 002 |   1.5 | 130 |
|  5 | 003 | 001 |   1.2 | 110 |
|  6 | 003 | 002 |   1.5 |  60 |
|  7 | 004 | 001 |   1.2 | 600 |
+----+-----+-----+-------+-----+
7 rows in set (0.06 sec)

mysql> select * from product;
+----+-----+-------+
| id | pno | pname |
+----+-----+-------+
|  1 | 001 | 产品1 |
|  2 | 002 | 产品2 |
|  3 | 003 | 产品3 |
|  4 | 004 | 产品4 |
+----+-----+-------+
4 rows in set (0.05 sec)

mysql> select * from product where pno in(select distinct pno from rec);
+----+-----+-------+
| id | pno | pname |
+----+-----+-------+
|  1 | 001 | 产品1 |
|  2 | 002 | 产品2 |
+----+-----+-------+
2 rows in set (0.08 sec)
```

没有销售的

```
mysql> select * from product where pno not in(select distinct pno from rec);
+----+-----+-------+
| id | pno | pname |
+----+-----+-------+
|  3 | 003 | 产品3 |
|  4 | 004 | 产品4 |
+----+-----+-------+
2 rows in set (0.08 sec)
```

##### 4，LIKE，NOT LIKE

LIKE，相关

%占位，不限数量

_一个占一个字符

```
mysql> insert user set uno='004',uname='赵云';
Query OK, 1 row affected (0.09 sec)
mysql> insert user set uno='005',uname='赵雅芝';
Query OK, 1 row affected (0.04 sec)
mysql> insert user set uno='006',uname='赵一曼';
Query OK, 1 row affected (0.11 sec)
mysql> select * from user;
+----+-----+--------+
| id | uno | uname  |
+----+-----+--------+
|  1 | 001 | 张三   |
|  2 | 002 | 李四   |
|  3 | 003 | 王五   |
|  4 | 004 | 赵云   |
|  5 | 005 | 赵雅芝 |
|  6 | 006 | 赵一曼 |
+----+-----+--------+
6 rows in set (0.07 sec)

mysql> select * from user where uname like'赵%';
+----+-----+--------+
| id | uno | uname  |
+----+-----+--------+
|  4 | 004 | 赵云   |
|  5 | 005 | 赵雅芝 |
|  6 | 006 | 赵一曼 |
+----+-----+--------+
3 rows in set (0.06 sec)

mysql> select * from user where uname like'赵__';
+----+-----+--------+
| id | uno | uname  |
+----+-----+--------+
|  5 | 005 | 赵雅芝 |
|  6 | 006 | 赵一曼 |
+----+-----+--------+
2 rows in set (0.09 sec)

```

##### 5，判断空值 IS NULL

```
mysql> alter table user change column uname uname char(21) null;
Query OK, 8 rows affected (1.22 sec)
Records: 8  Duplicates: 0  Warnings: 0

mysql> delete from user where uno='008';
Query OK, 1 row affected (0.07 sec)
mysql> select * from user;
+----+-----+--------+
| id | uno | uname  |
+----+-----+--------+
|  1 | 001 | 张三   |
|  2 | 002 | 李四   |
|  3 | 003 | 王五   |
|  4 | 004 | 赵云   |
|  5 | 005 | 赵雅芝 |
|  6 | 006 | 赵一曼 |
|  7 | 007 | 张飞   |
+----+-----+--------+
7 rows in set (0.06 sec)

mysql> insert user set uno='009';
Query OK, 1 row affected (0.06 sec)

mysql> select * from user;
+----+-----+--------+
| id | uno | uname  |
+----+-----+--------+
|  1 | 001 | 张三   |
|  2 | 002 | 李四   |
|  3 | 003 | 王五   |
|  4 | 004 | 赵云   |
|  5 | 005 | 赵雅芝 |
|  6 | 006 | 赵一曼 |
|  7 | 007 | 张飞   |
| 10 | 009 | NULL   |
+----+-----+--------+
8 rows in set (0.06 sec)

mysql> select * from user where uname is null;
+----+-----+-------+
| id | uno | uname |
+----+-----+-------+
| 10 | 009 | NULL  |
+----+-----+-------+
1 row in set (0.05 sec)
```

##### 6，多重条件 AND OR

```
mysql> select * from user where uname like '%张%' and uno in(select distinct gno from rec);
+----+-----+-------+
| id | uno | uname |
+----+-----+-------+
|  1 | 001 | 张三  |
+----+-----+-------+
1 row in set (0.09 sec)

```

##### 7，结合比较运算符的子查询

#### 五，GROUP BY 子句与分组数据

```
mysql> select price*num as 销售额, rec.*from rec;
+--------------------+----+-----+-----+-------+-----+
| 销售额             | id | Gno | Pno | price | num |
+--------------------+----+-----+-----+-------+-----+
| 360.00001430511475 |  1 | 001 | 001 |   1.2 | 300 |
|                300 |  2 | 001 | 002 |   1.5 | 200 |
|  204.0000081062317 |  3 | 002 | 001 |   1.2 | 170 |
|                195 |  4 | 002 | 002 |   1.5 | 130 |
| 132.00000524520874 |  5 | 003 | 001 |   1.2 | 110 |
|                 90 |  6 | 003 | 002 |   1.5 |  60 |
|  720.0000286102295 |  7 | 004 | 001 |   1.2 | 600 |
+--------------------+----+-----+-----+-------+-----+
7 rows in set (0.08 sec)
```
##### GROUP BY：

```
mysql> select sum(price*num) as 销售额, rec.*from rec group by gno;
+--------------------+----+-----+-----+-------+-----+
| 销售额             | id | Gno | Pno | price | num |
+--------------------+----+-----+-----+-------+-----+
|  660.0000143051147 |  1 | 001 | 001 |   1.2 | 300 |
|  399.0000081062317 |  3 | 002 | 001 |   1.2 | 170 |
| 222.00000524520874 |  5 | 003 | 001 |   1.2 | 110 |
|  720.0000286102295 |  7 | 004 | 001 |   1.2 | 600 |
+--------------------+----+-----+-----+-------+-----+
4 rows in set (0.08 sec)
```
##### HAVING 子句

过滤分组

```
mysql> select sum(price*num) as 销售额, rec.* from rec group by gno having 销售额>400;
+-------------------+----+-----+-----+-------+-----+
| 销售额            | id | Gno | Pno | price | num |
+-------------------+----+-----+-----+-------+-----+
| 660.0000143051147 |  1 | 001 | 001 |   1.2 | 300 |
| 720.0000286102295 |  7 | 004 | 001 |   1.2 | 600 |
+-------------------+----+-----+-----+-------+-----+
2 rows in set (0.07 sec)

```
##### ORDER BY 子句

排列顺序

使用降序排列：
```
mysql> select sum(price*num) as 销售额, rec.* from rec group by gno having 销售额>400 order by gno desc;
+-------------------+----+-----+-----+-------+-----+
| 销售额            | id | Gno | Pno | price | num |
+-------------------+----+-----+-----+-------+-----+
| 720.0000286102295 |  7 | 004 | 001 |   1.2 | 600 |
| 660.0000143051147 |  1 | 001 | 001 |   1.2 | 300 |
+-------------------+----+-----+-----+-------+-----+
2 rows in set (0.07 sec)

使用升序排列：

mysql> select sum(price*num) as 销售额, rec.* from rec group by gno having 销售额>400 order by gno asc;
+-------------------+----+-----+-----+-------+-----+
| 销售额            | id | Gno | Pno | price | num |
+-------------------+----+-----+-----+-------+-----+
| 660.0000143051147 |  1 | 001 | 001 |   1.2 | 300 |
| 720.0000286102295 |  7 | 004 | 001 |   1.2 | 600 |
+-------------------+----+-----+-----+-------+-----+
2 rows in set (0.07 sec)
```
##### LiMIT 子句

限制返回行数，0是第一行。

```
mysql> select sum(price*num) as 销售额, rec.* from rec group by gno limit 0,3;
+--------------------+----+-----+-----+-------+-----+
| 销售额             | id | Gno | Pno | price | num |
+--------------------+----+-----+-----+-------+-----+
|  660.0000143051147 |  1 | 001 | 001 |   1.2 | 300 |
|  399.0000081062317 |  3 | 002 | 001 |   1.2 | 170 |
| 222.00000524520874 |  5 | 003 | 001 |   1.2 | 110 |
+--------------------+----+-----+-----+-------+-----+
3 rows in set (0.06 sec)

```

### 第六节 视图

视图是从一个或多个表（或视图）导出的表。（外模式）

视图是数据库用户使用数据库的观点。

**视图是一张虚表。**

视图一经定义以后，就可以像表一样被查询、修改、删除和更新。

一，创建视图

表：user

```
mysql> select * from user;
+----+-----+--------+
| id | uno | uname  |
+----+-----+--------+
|  1 | 001 | 张三   |
|  2 | 002 | 李四   |
|  3 | 003 | 王五   |
|  4 | 004 | 赵云   |
|  5 | 005 | 赵雅芝 |
|  6 | 006 | 赵一曼 |
|  7 | 007 | 张飞   |
| 10 | 009 | NULL   |
+----+-----+--------+
8 rows in set (0.06 sec)
```

创建单表视图：

```
mysql> create view v_uname as select uname from user;
Query OK, 0 rows affected (0.16 sec)
```

创建跨表视图：

```
mysql> create view v_rec_user as select uname, rec.* from user,rec where user.uno=rec.gno;
Query OK, 0 rows affected (0.10 sec)
```

二，删除视图

```
mysql> drop view if exists v_uname;
Query OK, 0 rows affected (0.15 sec)
```

三，修改视图

```
mysql> alter view v_uname as select uname,pname from user,product where pno=uno;
Query OK, 0 rows affected (0.15 sec)
```

四，查看视图定义

```
mysql> show create view v_uname;
+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------+----------------------+
| View    | Create View                                                                                                                                                                                                                    | character_set_client | collation_connection |
+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------+----------------------+
| v_uname | CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `v_uname` AS select `user`.`uname` AS `uname`,`product`.`pname` AS `pname` from (`user` join `product`) where (`product`.`pno` = `user`.`uno`) | utf8mb4              | utf8mb4_0900_ai_ci   |
+---------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------+----------------------+
1 row in set (0.06 sec)
```

五，更新视图数据

六，查询视图数据

```
mysql> select * from V_uname;
+-------+-------+
| uname | pname |
+-------+-------+
| 张三  | 产品1 |
| 李四  | 产品2 |
| 王五  | 产品3 |
| 赵云  | 产品4 |
+-------+-------+
4 rows in set (0.07 sec)

```

