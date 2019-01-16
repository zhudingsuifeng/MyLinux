## DataGrip的使用

### datagrip start

从网站 https://www.jetbrains.com/zh/datagrip/ 下载安装软件，破解后开始使用。

File->DataSource 管理数据库驱动。

在Database视图中展开绿色的+号，添加数据库连接。

### datagrip连接oracle数据库

展开Database,点击右上角绿色+号，DataSource->Oracle.

```
Host: frps.hexcloud.cn
Port: 31733
SID: HEXDB
User: 数据库用户名
Password: 用户密码
```

SID是oracle中一个数据库的唯一标识符。是建立一个数据库时赋予的初始ID。
Test Connection成功后，Apply->OK.

```
SELECT DBMS_METADATA.GET_DDL('TABLE','POS_TRANSMST') FROM DUAL;
# 查看表POS_TRANSMST的结构
select count(*) from POS_TRANSMST;
# 查看POS_TRANSMST表的行数
```

database展开，顶部菜单栏最右边有个黑色的按钮，打开sql命令交互界面，就可以在里面输入sql语句了。

sql交互窗口，绿色箭头，执行sql语句,带时钟文档代表历史sql语句。

在oracle中的select 有关date条件时，需要指定格式，to_date('2013-2-26 11:07:25' , 'yyyy-mm-dd hh24:mi:ss')

select * from table where date = to_date('2013-2-26 11:07:25' , 'yyyy-mm-dd hh24:mi:ss');