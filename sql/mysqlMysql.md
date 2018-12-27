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

在mysql5.7.6之前，全文索引只支持英文全文索引，不支持中文全文索引。

从mysql5.7.6开始，mysql内置了ngram全文解析器，用来支持中文，全文索引支持InnoDB和MyISAM引擎。

```
# 若想使用全文本搜索需要在创建表的时候指定引擎,并对需要全文本搜索的列进行索引。
# 定义之后，mysql自动维护该索引。在增加，更新或删除行时，索引随之自动更新。
# 只能在类型为CHAR,VARCHAR或者TEXT的字段上创建全文索引。
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
# match() against(),match()匹配字段，必须是指定全文索引的字段。against()想要检索的内容。
# match()只能使用同一个表的字段，全文索引不支持跨多个表进行检索。
mysql> select * from school where match(ab) against('+SHANG' IN BOOLEAN MODE);
+------+--------------------+
| name | ab                 |
+------+--------------------+
| SHU  | SHANGHAIDAXUE      |
| FU   | SHANGHAIFUDANDAXUE |
+------+--------------------+
# 为school的name字段创建全文索引，定义索引的名称为idxname
mysql> alter table school add FULLTEXT index idxname(name) with parser ngram;
# 删除索引
mysql> alter table school drop index idxname;
# 创建完成索引，只有插入数据和更新数据才会触发创建全文索引，并不是设置了就会对以前数据生成全文索引。
```

**全文检索有两种模式**

1. 自然语言模式(natural language mode)

自然语言模式是mysql默认的全文检索模式。自然语言模式不能使用操作符，不能指定关键词必须出现或者不能出现等复杂查询。

2. BOOLEAN模式 (BOLLEAN MODE) 

BOOLEAN模式可以使用操作符，可以支持指定关键词必须出现或者必须不能出现或者关键词的权重高还是低等复杂查询。
