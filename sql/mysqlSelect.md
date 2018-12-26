## select

### select关键字

sql查询语句中用到的关键词主要包含6个，书写顺序大致为(关键字不区分大小写)

```
select--from--where--group by--having--order by
```

详细写法如下

```
SELECT  DISTINCT <select_list>
FROM <left_table>
<join_type> JOIN <right_table> ON <join_condition>
WHERE <where_condition>
GROUP BY <group_by_list>
with {cute | rollup}
HAVING <having_condition>
ORDER BY <order_by_list>
LIMIT <limit_number>
# JOIN ON and LIMIT 不是很常用
```

其中select和from是必须的，其他关键字是可选的，而关键字书写书序和执行顺序不同

```
FROM <left_table>
ON <join_condition>
<join_type> JOIN <right_table>
WHERE <where_condition>
GROUP BY <group_by_list>
HAVING <having_condition>
SELECT
DSTINCT <select_list>
ORDER BY <order_by_condition>
LIMIT <limit_number>
```

每步关键字执行的结果都会形成一个虚表，编号大的关键字执行的动作都是在编号小的关键字执行结果所得的虚表上进行。

### 查询准备

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| web                |
+--------------------+
mysql> use web;
mysql> show tables;
+---------------+
| Tables_in_web |
+---------------+
| student       |
+---------------+
```

### select from

```
# 查询所有列
mysql> select * from student;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | fly  |   24 |
+------+------+------+
# 查询指定列(name列)
mysql> select name from student;
+------+
| name |
+------+
| fly  |
+------+
```

### as

```
# 查询时指定别名(as 新名字)
mysql> select name as 'NAME' from student;
+------+
| NAME |
+------+
| fly  |
+------+
# 查询时添加常量列
mysql> select name, 'i am a student' as 'who' from student;
+------+----------------+
| name | who            |
+------+----------------+
| fly  | i am a student |
+------+----------------+
# 向表中添加一列
mysql> alter table student add column math int;
mysql> select * from student;
+------+------+------+------+
| id   | name | age  | math |
+------+------+------+------+
|    1 | fly  |   24 | NULL |
+------+------+------+------+
# 向表中添加多列
mysql> alter table student add column english int, add chinese int;
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | chinese |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 | NULL |    NULL |    NULL |
+------+------+------+------+---------+---------+
# 更改列名
mysql> alter table student change column chinese history int;
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 | NULL |    NULL |    NULL |
+------+------+------+------+---------+---------+
# 显示表结构
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
+---------+-------------+------+-----+---------+-------+
# 修改表列类型
mysql> alter table student modify history char(10);
mysql> desc student;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| id      | int(11)     | YES  |     | NULL    |       |
| name    | varchar(20) | YES  |     | NULL    |       |
| age     | int(11)     | YES  |     | NULL    |       |
| math    | int(11)     | YES  |     | NULL    |       |
| english | int(11)     | YES  |     | NULL    |       |
| history | char(10)    | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
# 删除表列
mysql> alter table student drop column history;
mysql> select * from student;
+------+------+------+------+---------+
| id   | name | age  | math | english |
+------+------+------+------+---------+
|    1 | fly  |   24 | NULL |    NULL |
+------+------+------+------+---------+
# 修改表名
mysql> alter table student rename people;
mysql> show tables;
+---------------+
| Tables_in_web |
+---------------+
| people        |
+---------------+
# 更新表数据
mysql> update student set math=80, english=68, history=100 where id =1;
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
+------+------+------+------+---------+---------+
# 向表中插入数据
mysql> insert into student values(2, 'test', 30, 60, 50, 80);
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    2 | test |   30 |   60 |      50 |      80 |
+------+------+------+------+---------+---------+
# 查询时和并列
mysql> select name, (math+english) as 'grade' from student;
+------+-------+
| name | grade |
+------+-------+
| fly  |   148 |
| test |   110 |
+------+-------+
```

### distinct

```
mysql> insert into student values(3, 'sky', 27, 80, 68, 80);
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    2 | test |   30 |   60 |      50 |      80 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
# 查询去重复记录
mysql> select distinct math from student;
== mysql> select distinct(math) from student;
+------+
| math |
+------+
|   80 |
|   60 |
+------+
```

### where

```
# =,>,<,>=,<=,<>,between
mysql> select * from student where math=60;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    2 | test |   30 |   60 |      50 |      80 |
+------+------+------+------+---------+---------+
mysql> select * from student where math>60;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
mysql> select * from student where math<80;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    2 | test |   30 |   60 |      50 |      80 |
+------+------+------+------+---------+---------+
mysql> select * from student where age between 20 and 29;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
# is 判断空字符串(NULL) is not
mysql> insert into student (id, name, age) values(4, 'temp', 50);
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    2 | test |   30 |   60 |      50 |      80 |
|    3 | sky  |   27 |   80 |      68 |      80 |
|    4 | temp |   50 | NULL |    NULL |    NULL |
+------+------+------+------+---------+---------+
mysql> select * from student where math is NULL;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    4 | temp |   50 | NULL |    NULL |    NULL |
+------+------+------+------+---------+---------+
mysql> delete from student where id=4;
mysql> select * from student;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    2 | test |   30 |   60 |      50 |      80 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
# and, or, in, not in
mysql> select * from student where age<28 and history>90;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
+------+------+------+------+---------+---------+
mysql> select * from student where age>28 or math=80;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    2 | test |   30 |   60 |      50 |      80 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
mysql> select * from student where age in (24,27);
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
mysql> select * from student where age not in (24,27);
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    2 | test |   30 |   60 |      50 |      80 |
+------+------+------+------+---------+---------+
# and 的优先级高于or优先级。遇到or和and并存的情况要注意优先级，圆括号可以改变mysql计算的优先级。
```

### like模糊查询与通配符_和%

其中**_代表一个未指定字符，%代表不定个未指定字符**。

```
mysql> select * from student where name like '%y%';
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
mysql> select * from student where name like '%s%';
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    2 | test |   30 |   60 |      50 |      80 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
```

### order by

```
# 升序排列
mysql> select * from student order by age asc;
# 默认升序排列
mysql> select * from student order by age;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
|    2 | test |   30 |   60 |      50 |      80 |
+------+------+------+------+---------+---------+
# 降序排列
mysql> select * from student order by age desc;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    2 | test |   30 |   60 |      50 |      80 |
|    3 | sky  |   27 |   80 |      68 |      80 |
|    1 | fly  |   24 |   80 |      68 |     100 |
+------+------+------+------+---------+---------+
# order by后面跟多个不同的排序字段
mysql> select * from student order by math asc, history desc;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    2 | test |   30 |   60 |      50 |      80 |
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
```
### limit

```
# select ... [limit 起始偏移量，行数];
mysql> select * from student limit 2,1;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
# 默认情况下，起始偏移量为0，只写记录行数就可以了。
mysql> select * from student limit 2;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    2 | test |   30 |   60 |      50 |      80 |
+------+------+------+------+---------+---------+
# limit配合其他关键字使用
mysql> select * from student order by age asc limit 2;
+------+------+------+------+---------+---------+
| id   | name | age  | math | english | history |
+------+------+------+------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 |
|    3 | sky  |   27 |   80 |      68 |      80 |
+------+------+------+------+---------+---------+
```

### sum() avg() max() min() count() 聚合查询

```
# sum()
mysql> select sum(age) from student;
+----------+
| sum(age) |
+----------+
|       81 |
+----------+
# avg()
mysql> select avg(math) from student;
+-----------+
| avg(math) |
+-----------+
|   73.3333 |
+-----------+
# max()
mysql> select max(history) from student;
+--------------+
| max(history) |
+--------------+
|          100 |
+--------------+
# min()
mysql> select min(age) from student;
+----------+
| min(age) |
+----------+
|       24 |
+----------+
# count()
mysql> select count(name) from student;
+-------------+
| count(name) |
+-------------+
|           3 |
+-------------+
```

### group by 分组查询

```
```

### having 分组查询之后筛选

```
```

### 子查询

```
```

### 表连接(连接查询)

```
# 内连接
# 外连接
## 左连接
## 右连接
```
