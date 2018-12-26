## 一些零散的mysql知识

### union

```
# union 指示mysql执行的多条select语句，并把输出组合成单个查询结果集。
mysql> select * from student where name='fly'
    -> union
    -> select * from student where age>30;
+------+------+------+------+---------+---------+---------+
| id   | name | age  | math | english | history | teacher |
+------+------+------+------+---------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 | liu     |
|    4 | snow |   40 |   70 |      80 |      90 | wu      |
|    6 | kaow |   33 |   50 |      60 |      70 | xu      |
+------+------+------+------+---------+---------+---------+
# union 中每个查询必须包含相同的列，表达式或聚集函数。
# union 默认去重复，union all 不去重复。
mysql> select * from student where name='fly'
    -> union
    -> select * from student where age>30
    -> order by english;
+------+------+------+------+---------+---------+---------+
| id   | name | age  | math | english | history | teacher |
+------+------+------+------+---------+---------+---------+
|    6 | kaow |   33 |   50 |      60 |      70 | xu      |
|    1 | fly  |   24 |   80 |      68 |     100 | liu     |
|    4 | snow |   40 |   70 |      80 |      90 | wu      |
+------+------+------+------+---------+---------+---------+
# 使用union组合查询时，只能使用一条order by子句，它必须出现在最后一条select语句之后。
```

### 全文本搜索

并非所有引擎都支持全文本搜索，常用的MyISAM引擎支持全文本搜索，InnoDB引擎不支持。--------------这里有问题---------------------------

```
# 若想使用全文本搜索需要在创建表的时候指定引擎,并对需要全文本搜索的列进行索引。
# 定义之后，mysql自动维护该索引。在增加，更新或删除行时，索引随之自动更新。
mysql> create table school(
    -> name varchar(20),
    -> ab text,
    -> FULLTEXT(ab) WITH PARSER ngram
    -> )ENGINE=INNODB;
# 不要在导入数据时使用FULLTEXT，更新索引会花费额外时间。
# 应该先导入数据，然后修改表，定义FULLTEXT，这样可以更快的导入数据。
mysql> insert into school values('SHU','SHANGHAIDAXUE'),
    -> ('QUST','QINGDAOKEJIDAXUE'),('FU','SHANGHAIFUDANDAXUE');
mysql> select * from school;
+------+--------------------+
| name | ab                 |
+------+--------------------+
| SHU  | SHANGHAIDAXUE      |
| QUST | QINGDAOKEJIDAXUE   |
| FU   | SHANGHAIFUDANDAXUE |
+------+--------------------+
mysql> select * from school where match(ab) against('+SHANG' IN BOOLEAN MODE);
+------+--------------------+
| name | ab                 |
+------+--------------------+
| SHU  | SHANGHAIDAXUE      |
| FU   | SHANGHAIFUDANDAXUE |
+------+--------------------+
```
