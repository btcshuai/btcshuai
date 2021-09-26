# MySQL create table as与create table like对比

在MySQL数据库中，关于表的克隆有多种方式，比如我们可以使用create table ..as .. ，也可以使用create table .. like ..方式。然而这2种不同的方式还是有些差异的，他的差异到底在哪里呢，本文通过演示对此展开描述。


1、mysql sakila表上的结构
```
--actor表状态
robin@localhost[sakila]> show table status like 'actor'\G
*************************** 1. row ***************************
           Name: actor
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 200
 Avg_row_length: 81
    Data_length: 16384
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: 201
    Create_time: 2014-12-25 13:08:25
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options: 
        Comment: 
1 row in set (0.00 sec)

--actor表索引
robin@localhost[sakila]> show index from actor\G
*************************** 1. row ***************************
        Table: actor
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: actor_id
    Collation: A
  Cardinality: 200
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 2. row ***************************
        Table: actor
   Non_unique: 1
     Key_name: idx_actor_last_name
 Seq_in_index: 1
  Column_name: last_name
    Collation: A
  Cardinality: 200
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
2 rows in set (0.00 sec)

--actor表结构
robin@localhost[sakila]> desc actor;
+-------------+----------------------+------+-----+-------------------+-----------------------------+
| Field       | Type                 | Null | Key | Default           | Extra                       |
+-------------+----------------------+------+-----+-------------------+-----------------------------+
| actor_id    | smallint(5) unsigned | NO   | PRI | NULL              | auto_increment              |
| first_name  | varchar(45)          | NO   |     | NULL              |                             |
| last_name   | varchar(45)          | NO   | MUL | NULL              |                             |
| last_update | timestamp            | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+-------------+----------------------+------+-----+-------------------+-----------------------------+
4 rows in set (0.00 sec)

```
2、使用create table as方式克隆表

```
robin@localhost[sakila]> create table actor_as as select * from actor;
Query OK, 200 rows affected (0.06 sec)
Records: 200  Duplicates: 0  Warnings: 0

robin@localhost[sakila]> desc actor_as;
+-------------+----------------------+------+-----+-------------------+-----------------------------+
| Field       | Type                 | Null | Key | Default           | Extra                       |
+-------------+----------------------+------+-----+-------------------+-----------------------------+
| actor_id    | smallint(5) unsigned | NO   |     | 0                 |                             |
| first_name  | varchar(45)          | NO   |     | NULL              |                             |
| last_name   | varchar(45)          | NO   |     | NULL              |                             |
| last_update | timestamp            | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+-------------+----------------------+------+-----+-------------------+-----------------------------+
--从上面的结果可以看出新表缺少了key信息，以及自增列属性 auto_increment

robin@localhost[sakila]> show table status like 'actor_as'\G
*************************** 1. row ***************************
           Name: actor_as
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 200
 Avg_row_length: 81
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2015-01-19 10:42:53
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options: 
        Comment: 
1 row in set (0.00 sec)

--从上面的表结构可以看出，表状态与原表等同，仅仅是创建时间的差异，
robin@localhost[sakila]> show index from actor_as \G
Empty set (0.00 sec)

--从上面的查询可以看出，新表没有任何索引
```

3、使用create table like方式克隆表

```
robin@localhost[sakila]> create table actor_like like actor;
Query OK, 0 rows affected (0.01 sec)

robin@localhost[sakila]> select count(*) from actor_like;
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)
--从上面的查询可知，使用like方式没有任何数据被克隆到新表

robin@localhost[sakila]> desc actor_like;
+-------------+----------------------+------+-----+-------------------+-----------------------------+
| Field       | Type                 | Null | Key | Default           | Extra                       |
+-------------+----------------------+------+-----+-------------------+-----------------------------+
| actor_id    | smallint(5) unsigned | NO   | PRI | NULL              | auto_increment              |
| first_name  | varchar(45)          | NO   |     | NULL              |                             |
| last_name   | varchar(45)          | NO   | MUL | NULL              |                             |
| last_update | timestamp            | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+-------------+----------------------+------+-----+-------------------+-----------------------------+

robin@localhost[sakila]> show index from actor_like\G
*************************** 1. row ***************************
        Table: actor_like
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: actor_id
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 2. row ***************************
        Table: actor_like
   Non_unique: 1
     Key_name: idx_actor_last_name
 Seq_in_index: 1
  Column_name: last_name
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
2 rows in set (0.00 sec)

--从上面的表结构以及索引信息可以看到，表除了没有数据之外，结构被进行了完整克隆
--下面为like方式的表插入数据
robin@localhost[sakila]> insert into actor_like select * from actor;
Query OK, 200 rows affected (0.03 sec)
Records: 200  Duplicates: 0  Warnings: 0

robin@localhost[sakila]> show index from actor_like\G
*************************** 1. row ***************************
        Table: actor_like
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: actor_id
    Collation: A
  Cardinality: 200
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 2. row ***************************
        Table: actor_like
   Non_unique: 1
     Key_name: idx_actor_last_name
 Seq_in_index: 1
  Column_name: last_name  -- Author: Leshami
    Collation: A          -- Blog  : http://blog.csdn.net/leshami 
  Cardinality: 200
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
2 rows in set (0.00 sec)
--上面的查询中新表的索引统计信息被收集

robin@localhost[sakila]> explain select * from actor where last_name like 'A%';
+----+-------------+-------+-------+---------------------+---------------------+---------+------+------+-----------------------+
| id | select_type | table | type  | possible_keys       | key                 | key_len | ref  | rows | Extra                 |
+----+-------------+-------+-------+---------------------+---------------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | actor | range | idx_actor_last_name | idx_actor_last_name | 137     | NULL |    7 | Using index condition |
+----+-------------+-------+-------+---------------------+---------------------+---------+------+------+-----------------------+
1 row in set (0.00 sec)

robin@localhost[sakila]> explain select * from actor_like where last_name like 'A%';
+----+-------------+------------+-------+---------------------+---------------------+---------+------+------+-----------------------+
| id | select_type | table      | type  | possible_keys       | key                 | key_len | ref  | rows | Extra                 |
+----+-------------+------------+-------+---------------------+---------------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | actor_like | range | idx_actor_last_name | idx_actor_last_name | 137     | NULL |    7 | Using index condition |
+----+-------------+------------+-------+---------------------+---------------------+---------+------+------+-----------------------+
1 row in set (0.00 sec)
--从上面的执行计划可以看出，like方式建表与原表使用了相同的执行计划
```
4、基于myisam引擎进行create table like方式克隆
```
robin@localhost[sakila]> alter table actor_like engine=myisam;
Query OK, 200 rows affected (0.03 sec)
Records: 200  Duplicates: 0  Warnings: 0

robin@localhost[sakila]> show table status like 'actor_like'\G
*************************** 1. row ***************************
           Name: actor_like
         Engine: MyISAM
        Version: 10
     Row_format: Dynamic
           Rows: 200
 Avg_row_length: 25
    Data_length: 5016
Max_data_length: 281474976710655
   Index_length: 7168
      Data_free: 0
 Auto_increment: 201
    Create_time: 2015-01-19 11:19:55
    Update_time: 2015-01-19 11:19:55
     Check_time: 2015-01-19 11:19:55
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options: 
        Comment: 
1 row in set (0.00 sec)

robin@localhost[sakila]> create table actor_like_isam like actor_like;
Query OK, 0 rows affected (0.01 sec)

robin@localhost[sakila]> insert into actor_like_isam select * from actor_like;
Query OK, 200 rows affected (0.00 sec)
Records: 200  Duplicates: 0  Warnings: 0

robin@localhost[sakila]> insert into actor_like_isam select * from actor_like;
Query OK, 200 rows affected (0.00 sec)
Records: 200  Duplicates: 0  Warnings: 0

robin@localhost[sakila]> show index from actor_like_isam\G
*************************** 1. row ***************************
        Table: actor_like_isam
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: actor_id
    Collation: A
  Cardinality: 200
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 2. row ***************************
        Table: actor_like_isam
   Non_unique: 1
     Key_name: idx_actor_last_name
 Seq_in_index: 1
  Column_name: last_name
    Collation: A
  Cardinality: 100
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
2 rows in set (0.00 sec)

robin@localhost[sakila]> explain select * from actor_like_isam where last_name like 'A%';
+----+-------------+-----------------+-------+---------------------+---------------------+---------+------+------+-----------------------+
| id | select_type | table           | type  | possible_keys       | key                 | key_len | ref  | rows | Extra                 |
+----+-------------+-----------------+-------+---------------------+---------------------+---------+------+------+-----------------------+
|  1 | SIMPLE      | actor_like_isam | range | idx_actor_last_name | idx_actor_last_name | 137     | NULL |    6 | Using index condition |
+----+-------------+-----------------+-------+---------------------+---------------------+---------+------+------+-----------------------+
1 row in set (0.00 sec)

--从上面的测试可以看出基于myisam引擎方式对原表结构也是使用完成克隆方式
```
5、小结
a、create table like方式会完整地克隆表结构，但不会插入数据，需要单独使用insert into或load data方式加载数据
b、create table as  方式会部分克隆表结构，完整保留数据
c、create table as select .. where 1=0 会克隆部分表结构，但不克隆数据。
d、如果启用了gtid，create table as方式不被支持。收到ERROR 1786 (HY000): CREATE TABLE ... SELECT is forbidden when @@GLOBAL.ENFORCE_GTID_CONSISTENCY = 1.
————————————————
版权声明：本文为CSDN博主「Leshami」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/leshami/article/details/46800847
