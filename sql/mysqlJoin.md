### join表连接(连接查询)

等值联结基于两个表之间的相等测试，这种联结也称为内部联结。

```
# 在处理多个表时，子查询只有在结果来自一个表时才有用。
# 但如果需要显示两个表或多个表中的数据，这时就必须使用连接(join)操作。
# 连接的基本思想是把两个或多个表当做一个新的表来操作。
mysql> select student.name,student.age,teacher.name from student,teacher 
     > where student.teacher=teacher.name;
+------+------+------+
| name | age  | name |
+------+------+------+
| fly  |   24 | liu  |
| test |   30 | wu   |   
| sky  |   27 | liu  |
| snow |   40 | wu   |   
+------+------+------+
mysql> select student.*,teacher.age from student,teacher 
     > where student.teacher=teacher.name;
+------+------+------+------+---------+---------+---------+------+
| id   | name | age  | math | english | history | teacher | age  |
+------+------+------+------+---------+---------+---------+------+
|    1 | fly  |   24 |   80 |      68 |     100 | liu     |   50 |
|    2 | test |   30 |   60 |      50 |      80 | wu      |   60 |
|    3 | sky  |   27 |   80 |      68 |      80 | liu     |   50 |
|    4 | snow |   40 |   70 |      80 |      90 | wu      |   60 |
+------+------+------+------+---------+---------+---------+------+
# 内连接(table1 join table2 on 条件)
mysql> select student.name,student.age,teacher.name from student
     > join teacher
     > on student.teacher=teacher.name;
+------+------+------+
| name | age  | name |
+------+------+------+
| fly  |   24 | liu  |
| test |   30 | wu   |
| sky  |   27 | liu  |
| snow |   40 | wu   |
+------+------+------+
mysql> select student.*,teacher.age from student
     > join teacher
     > on student.teacher=teacher.name;
+------+------+------+------+---------+---------+---------+------+
| id   | name | age  | math | english | history | teacher | age  |
+------+------+------+------+---------+---------+---------+------+
|    1 | fly  |   24 |   80 |      68 |     100 | liu     |   50 |
|    2 | test |   30 |   60 |      50 |      80 | wu      |   60 |
|    3 | sky  |   27 |   80 |      68 |      80 | liu     |   50 |
|    4 | snow |   40 |   70 |      80 |      90 | wu      |   60 |
+------+------+------+------+---------+---------+---------+------+
# 左外链接(包含join左边表中的记录，甚至右边表中没有和他匹配的记录)
mysql> select student.id,student.name,teacher.* from student
     > left join teacher
     > on student.id=teacher.id;
+------+------+------+------+------+
| id   | name | id   | name | age  |
+------+------+------+------+------+
|    1 | fly  |    1 | liu  |   50 |
|    2 | test |    2 | wu   |   60 |
|    3 | sky  | NULL | NULL | NULL |
|    4 | snow | NULL | NULL | NULL |
+------+------+------+------+------+
# 右外链接(包含join右边表中的记录，甚至左边表中没有和他匹配的记录)
mysql> select teacher.*,student.name from teacher
     > right join student
     > on teacher.id=student.id;
+------+------+------+------+
| id   | name | age  | name |
+------+------+------+------+
|    1 | liu  |   50 | fly  |
|    2 | wu   |   60 | test |
| NULL | NULL | NULL | sky  |
| NULL | NULL | NULL | snow |
+------+------+------+------+
# full全连接，将左右两个表的数据全部返回,mysql不支持full join.
```

mysql在运行时关联指定的每个表以处理联结。联结的表越多，性能下降越厉害。
