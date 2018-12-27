## Index(索引)

索引可以大大提高mysql的检索速度。

索引分单列索引和组合索引。单列索引，即一个索引只包含单个列，一个表可以有多个单列索引，但这不是组合索引。组合索引，即一个索引包含多个列。

实际上，索引也是一张表，该表保存了主见与索引字段，并指向实体表的记录。

### 索引类型

mysql目前主要有以下几种索引类型:FULLTEXT,HASH,BTREE,RTREE.

1. FULLTEXT即全文索引。

2. HASH索引可以一次定位，不需要像树形索引那样逐层查找，因此具有极高的效率。

3. BTREE二叉树索引，每次查询都从树的入口root开始查找，时间复杂度O(logN).

4. RTREE相对于BTREE优势在于范围查找。

### 索引种类

普通索引：仅加速查询

唯一索引：加速查询+列值唯一(可以有NULL)

主键索引：加速查询+列值唯一(不可以有NULL)+表中只有一个

组合索引：多列值组成一个索引，专门用于组合搜索，其效率大于索引合并

全文索引：对文本的内容进行分词，进行搜索
```
mysql> desc student;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| id      | int(11)     | YES  |     | NULL    |       |
| name    | varchar(20) | YES  |     | NULL    |       |
| age     | int(11)     | YES  |     | NULL    |       |
| math    | int(11)     | YES  |     | NULL    |       |
| english | int(11)     | YES  |     | NULL    |       |
| history | int(11)     | YES  |     | NULL    |       |
| teacher | varchar(20) | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
# 创建索引
mysql> create index test on student(name);
mysql> desc student;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| id      | int(11)     | YES  |     | NULL    |       |
| name    | varchar(20) | YES  | MUL | NULL    |       |
| age     | int(11)     | YES  |     | NULL    |       |
| math    | int(11)     | YES  |     | NULL    |       |
| english | int(11)     | YES  |     | NULL    |       |
| history | int(11)     | YES  |     | NULL    |       |
| teacher | varchar(20) | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
# 注意Key列的值变化
# 通过修改表结构创建索引
mysql> alter table student add index test(name);
# 创建表时直接指定索引
mysql> create table student(
    -> id int,
    -> name varchar(20),
    -> index test(name)
    -> );
# 删除索引
mysql> drop index test on student;
# 修改索引
# mysql中并没有直接修改索引的命令，修改索引就是删除重建。
--先删除
mysql> alter table student drop index test;
--再以修改后的内容创建同名索引
mysql> create index test on student(name);
# 查看索引
mysql> desc student;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| id      | int(11)     | YES  |     | NULL    |       |
| name    | varchar(20) | YES  | MUL | NULL    |       |
| age     | int(11)     | YES  |     | NULL    |       |
| math    | int(11)     | YES  |     | NULL    |       |
| english | int(11)     | YES  |     | NULL    |       |
| history | int(11)     | YES  |     | NULL    |       |
| teacher | varchar(20) | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
mysql> show create table student;
mysql> show index from student;
```

### 慢查询日志

```
# 显示慢查询日志的超时时间，存放慢查询日志的路径。
mysql> show global variables like '%slow%';
+---------------------------+-----------------------------+
| Variable_name             | Value                       |
+---------------------------+-----------------------------+
| log_slow_admin_statements | OFF                         |
| log_slow_slave_statements | OFF                         |
| slow_launch_time          | 2                           |
| slow_query_log            | OFF                         |
| slow_query_log_file       | /var/lib/mysql/sky-slow.log |
+---------------------------+-----------------------------+
# 开启慢查询日志
mysql> set global slow_query_log=on;
# 查看慢查询日志的设置时间
mysql> show global variables like '%long%';
+----------------------------------------------------------+-----------+
| Variable_name                                            | Value     |
+----------------------------------------------------------+-----------+
| long_query_time                                          | 10.000000 |
| performance_schema_events_stages_history_long_size       | 10000     |
| performance_schema_events_statements_history_long_size   | 10000     |
| performance_schema_events_transactions_history_long_size | 10000     |
| performance_schema_events_waits_history_long_size        | 10000     |
+----------------------------------------------------------+-----------+
# 修改慢查询时间，只要超过设置的时间，查询日志就会进入到慢查询日志
mysql> set global long_query_time=1;
# 打印执行计划，分析查询缓慢的原因
mysql> explain select * from student;
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table   | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | student | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | NULL  |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
# 优化查询，建立索引(通过explain分析是查询的哪一部分制约了查询速度，对其创建索引)
mysql> create index test on student(name);
mysql> select * from student;
+------+---------+------+------+---------+---------+---------+
| id   | name    | age  | math | english | history | teacher |
+------+---------+------+------+---------+---------+---------+
|    1 | fly     |   24 |   80 |      68 |     100 | liu     |
|    2 | test    |   30 |   60 |      50 |      80 | wu      |
|    3 | sky     |   27 |   80 |      68 |      80 | liu     |
|    4 | snow    |   40 |   70 |      80 |      90 | wu      |
|    5 | flasky  |   18 |   23 |      55 |      77 | xu      |
|    6 | kaow    |   27 |  233 |     505 |      77 | xu      |
|    7 | trigger |   40 |   13 |     111 |      60 | wu      |
+------+---------+------+------+---------+---------+---------+
7 rows in set (0.00 sec)
# 在结果后面，跟着显示的行数，和所需要的时间
```
