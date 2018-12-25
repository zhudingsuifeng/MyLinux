## How to install mysql and use mysql onto ubuntu18.04

```
$sudo su
#apt update
#apt install mysql-server
#apt install mysql-client
#apt install libmysqlclient-dev
#apt autoremove --purge mysql-server #卸载mysql-server
#mysql   #需要root权限才能正常登陆，不然报错
mysql>show databases;
mysql>status
# 查看数据库状态
--------------
mysql  Ver 14.14 Distrib 5.7.24, for Linux (x86_64) using  EditLine wrapper

Connection id:		7
Current database:
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.7.24-0ubuntu0.18.04.1 (Ubuntu)
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	latin1
Db     characterset:	latin1
Client characterset:	utf8
Conn.  characterset:	utf8
UNIX socket:		/var/run/mysqld/mysqld.sock
Uptime:			1 hour 2 min 35 sec

Threads: 1  Questions: 34  Slow queries: 0  Opens: 114  Flush tables: 1  Open tables: 107  Queries per second avg: 0.009
--------------
mysql>use mysql;
# 使用数据库mysql(切换数据库)
mysql>select database() mydb;
# 查看当前登录的数据库(就是前面use <database>中的数据库)
+-------+
| mydb  |
+-------+
| mysql |
+-------+
mysql>select user,host,plugin from user;
+------------------+-----------+-----------------------+
| user             | host      | plugin                |
+------------------+-----------+-----------------------+
| root             | localhost | auth_socket           |
| mysql.session    | localhost | mysql_native_password |
| mysql.sys        | localhost | mysql_native_password |
| debian-sys-maint | localhost | mysql_native_password |
+------------------+-----------+-----------------------+
4 rows in set (0.00 sec)
mysql>create user 'fly'@'%' identified by 'passwd';
# create user 用户名@登陆IP identified by "密码";
# 创建用户，登录IP，如果指定只能本地登录为"localhost",如果想要远程登录为"%".
mysql>select host,user,plugin from user;
+-----------+------------------+-----------------------+
| host      | user             | plugin                |
+-----------+------------------+-----------------------+
| localhost | root             | auth_socket           |
| localhost | mysql.session    | mysql_native_password |
| localhost | mysql.sys        | mysql_native_password |
| localhost | debian-sys-maint | mysql_native_password |
| %         | fly              | mysql_native_password |
+-----------+------------------+-----------------------+
mysql>show grants for 'fly'@'%';
# 查看用户权限
+---------------------------------+
| Grants for fly@%                |
+---------------------------------+
| GRANT USAGE ON *.* TO 'fly'@'%' |
+---------------------------------+
mysql>grant all privileges on web.* to 'fly'@'%';
# 给用户授予权限
mysql>show grants for 'fly'@'%';
+----------------------------------------------+
| Grants for fly@%                             |
+----------------------------------------------+
| GRANT USAGE ON *.* TO 'fly'@'%'              |
| GRANT ALL PRIVILEGES ON `web`.* TO 'fly'@'%' |
+----------------------------------------------+
mysql>exit
#exit
$mysql -u fly -p
mysql>show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
+--------------------+
mysql>exit
#mysql
mysql>drop user 'fly'@'%';
# 删除用户
mysql>drop user test;
# 后面直接跟用户名就可以。
mysql> select user,host from mysql.user;
# from 后面可以跟database.table
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| debian-sys-maint | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
#mysql
mysql>create database web;
# 创建数据库
mysql>show databases;
# 查看数据库
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| web                |
+--------------------+
mysql>drop database web;
# 删除数据库
mysql>show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
mysql>grant all privileges on web.* to fly@'%' identified by 'passwd';
# 创建用户同时授予权限
# grant 权限 on 数据库.* to 用户名@登陆主机 identified by "密码";
mysql>exit
#mysql -u fly -p 
mysql>show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| web                |
+--------------------+
mysql>set password for 'username'@'%'=password('密码');
# 设置指定用户username的密码
# 创建表
mysql>create table student(
    -> id int,
    -> name varchar(20),
    -> age int
    -> );
# 插入数据
mysql>insert into student(id, name, age) values(1, 'fly', 24);
mysql>insert into student(id, name, age) values(2, 'test', 24);
# 查询数据[select](https://github.com/zhudingsuifeng/platform/blob/master/sql/select.md)
mysql>select * from student;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | fly  |   24 |
|    2 | test |   24 |
+------+------+------+
# 查询这部分还有很多细节值的仔细研究
# 更新数据
mysql>update student set age=30 where id=2;
mysql>select * from student;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | fly  |   24 |
|    2 | test |   30 |
+------+------+------+
# 删除数据
mysql>delete from student where id=2;
mysql>select * from student;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | fly  |   24 |
+------+------+------+
# 删除所有数据
mysql>delete from student;
# 删除表
mysql>drop table student;
# 查看表
mysql>show tables;
```
