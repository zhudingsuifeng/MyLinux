## How to install mysql and use mysql onto ubuntu18.04

```
$sudo su
#apt update
#apt install mysql-server
#apt install mysql-client
#apt install libmysqlclient-dev
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
mysql>
```
```
mysql>create user 'fly'@'%' identified by 'passwd';
```

fly 是用户名。

@ 后面跟要链接到mysql数据库的客户端的IP地址，%代表全部IP均可链接。

passwd 是要给fly用户设置的用户密码。


