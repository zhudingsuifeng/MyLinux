## 存储过程

存储过程简单来说，就是为以后的使用而保存的一条或多条mysql语句的集合。

```
# 临时更改命令行语句分割符(delimiter)
# 更改分隔符为//,确保存储过程中以;结尾的语句不会被执行
mysql> DELIMITER //
# 创建存储过程
mysql> CREATE PROCEDURE student()
    -> BEGIN
    -> SELECT * FROM student;
    -> END //
# END存储过程定义结束
# 语句分隔符更改回;
mysql> DELIMITER ;
# 调用存储过程
mysql> CALL student();
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
# 删除存储过程
# drop procedure student;
# 更改子句分隔符
mysql> DELIMITER //
# 创建查询过程，带参数(IN(传递给存储过程)OUT(从存储过程传出)INOUT(对存储过程传入和传出))
mysql> CREATE PROCEDURE student(
    -> IN sname varchar(20),
    -> OUT tname varchar(20)
    -> )
    -> BEGIN
    -> SELECT * FROM student where name=sname;
    -> SELECT teacher from student where name=sname INTO tname;
    -> END//
mysql> DELIMITER ;
# 调用存储过程
mysql> call student('fly',@teacher);
+------+------+------+------+---------+---------+---------+
| id   | name | age  | math | english | history | teacher |
+------+------+------+------+---------+---------+---------+
|    1 | fly  |   24 |   80 |      68 |     100 | liu     |
+------+------+------+------+---------+---------+---------+
# @teacher 由存储过程填写
mysql> select @teacher;
+----------+
| @teacher |
+----------+
| liu      |
+----------+
# 显示检查存储过程
mysql> show create procedure student;
```

## 使用游标

游标实际上是一种能从包括多条数据记录的结果集中每次提取一条记录的机制。

游标充当指针的作用。

尽管游标能便利结果中的所有行，但他一次只指向一行。

```
mysql> DELIMITER //
# 游标依赖于过程，过程结束，游标消失
mysql> create procedure student()
    -> begin
    -> declare stname varchar(20);
    -> declare test cursor for
    -> select name from student;
    -> open test;
    -> fetch next from test into stname;
    -> select stname;
    -> close test;
    -> END//
mysql> DELIMITER ;
mysql> CALL student();
+--------+
| stname |
+--------+
| fly    |
+--------+
# 要把游标结果存储到变量，再select变量，不然不会显示结果
```
