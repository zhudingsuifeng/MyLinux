## TRIGGER

触发器是mysql响应DELETE,INSERT,UPDATE语句而自动执行的mysql语句。

```
# 创建触发器
mysql> CREATE TRIGGER tst AFTER INSERT ON student FOR EACH ROW 
    -> SELECT NEW.name INTO @news;
mysql> insert into student values(7,'trigger',40,13,111,60,'wu');
mysql> select @news;
+---------+
| @news   |
+---------+
| trigger |
+---------+
# 删除触发器
mysql> drop trigger tst;
# 6大触发器(INSERT,UPDATE,DELETE之前和之后)
```
