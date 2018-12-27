## view

视图是虚拟的表。与包含数据的表不一样，视图只包含使用动态检索数据的查询。

视图本身不包含数据，因此他们返回的数据是从其他表中检索出来的。在添加或更改这些表中的数据时，视图将返回改变过的数据。

```
mysql> select * from student;
+------+--------+------+------+---------+---------+---------+
| id   | name   | age  | math | english | history | teacher |
+------+--------+------+------+---------+---------+---------+
|    1 | fly    |   24 |   80 |      68 |     100 | liu     |
|    2 | test   |   30 |   60 |      50 |      80 | wu      |
|    3 | sky    |   27 |   80 |      68 |      80 | liu     |
|    4 | snow   |   40 |   70 |      80 |      90 | wu      |
|    5 | flasky |   18 |   23 |      55 |      77 | xu      |
|    6 | kaow   |   27 |  233 |     505 |      77 | xu      |
+------+--------+------+------+---------+---------+---------+
mysql> select * from teacher;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | liu  |   50 |
|    2 | wu   |   60 |
+------+------+------+
# create view 创建视图，多张表中有相同的列名注意使用别名(as),解决冲突。
mysql> create view teach as
    -> select student.name as sname, student.age as sage,teacher.name as tname
    -> from student,teacher
    -> where student.teacher=teacher.name;
# 视图和表类似，但是视图不能索引，也不能有关联的触发器或默认值。
mysql> select * from teach;
+-------+------+-------+
| sname | sage | tname |
+-------+------+-------+
| fly   |   24 | liu   |
| test  |   30 | wu    |
| sky   |   27 | liu   |
| snow  |   40 | wu    |
+-------+------+-------+
# show create view viewnam,查看创建视图的语句。
mysql> show create view teach;
# 删除视图
mysql> drop view teach;
```

视图本身没有数据，更新(insert,update和delete)视图，实际上是在更新基表。

如果mysql不能正确地确定被更新的数据，则不允许更新。

即视图中包含以下操作，则不能更新视图：

分组(group by having)

联结

子查询

并

聚集函数(min(),count(),sum()...)

DISTINCT
