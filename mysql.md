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
mysql>use mysql;
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
mysql>create database web;
mysql>show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| web                |
+--------------------+
mysql>grant all privileges on web.* fly@'%' identified by 'passwd';
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
```
