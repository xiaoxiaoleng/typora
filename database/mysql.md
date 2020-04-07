### 数据库回滚

##### 1、Step1: 确认当前binlog

```
show master status;
```

##### 2、将binlog转换为text，确认时间点

```
mysqlbinlog mysql-bin.000088 > test1.txt
```

##### 3、获取特定数据库

```shell
./mysqlbinlog --stop-position=9934919 -d portal --skip-gtids /opt/dbdata/mysql/mysql_bin.000002 > portal.sql
```

##### 4、查看二进制日志文件（解码）

```shell
./mysqlbinlog --no-defaults --base64-output=decode-rows /opt/dbdata/mysql/mysql_bin.000002 > bak.txt
```

##### 5、将数据恢复到时间点

```shell
./mysql -u dbuser -p -e "source ./portal.sql"
```

```shell
./mysqlbinlog --stop-datetime="2019-12-23 15:30:34" -d portal --skip-gtids /opt/dbdata/mysql/mysql_bin.000002 | mysql -udbuser -p
```

##### 注意事项

```
进行时间点备份之前 先做全量备份
```

##### 备份所有数据库

```shell
./mysqldump -udbuser -pDBuser123! --all-databases > all_database.sql
```

##### 备份单个数据库的所有表

```shell
./mysqldump -udbuser -pDBuser123! --set-gtid-purged=off --databases portal>portal_bak.sql
```



##### 查看mysqld工作目录

```shell
ps -ef | grep defaults | grep 3306 | awk '{print $8}'
```

##### 判断是否为主节点

```mysql
show master status;
show slave status;
```



#### 输出结果展示

```mysql
使用 \G , 比较推荐这个用法，把行转化成列显示
show slave status \G
查询语句使用 \G 结尾，不需要 ;了。推荐经常用这种写法，很实用。
```



##### Mysql conf开启审计统计分析

```
/etc/my.cnf 里面再加上一行 
binlog_rows_query_log_events=ON
这样就能在row格式下记录下具体的执行的SQL语句了
```



##### 导出表结构、数据

```shell
1、导出数据库dbname的表结构（其中用戶名為root,密码為dbpasswd,生成的脚本名為db.sql）
  mysqldump -uroot -pdbpasswd -d dbname >db.sql;

2、导出数据库dbname某张表(test)结构
  mysqldump -uroot -pdbpasswd -d dbname test>db.sql;

3、导出数据库dbname所有表结构及表数据（不加-d）
  mysqldump -uroot -pdbpasswd dbname >db.sql;

4、导出数据库dbname某张表(test)结构及表数据（不加-d）
  mysqldump -uroot -pdbpasswd dbname test>db.sql;
```

##### 删除数据库

```mysql
例如删除名为 RUNOOB 的数据库：
mysql> drop database RUNOOB;

也可使用 mysql mysqladmin 命令在终端来执行删除命令。
以下实例删除数据库 RUNOOB：
[root@host]# ./mysqladmin -u root -p drop RUNOOB
Enter password:******
```

##### 查看所有数据库

```mysql
1、显示数据库列表。 
show databases; 
2、显示库中的数据表： 
use mysql;
show tables; 
3、显示数据表的结构： 
describe 表名; 
```

##### 刷新数据库

```
flush privileges;
```

##### 还原数据库

```
还原MySQL数据库的命令(需要确保portal库存在./mysqladmin -u dbuser -p -h localhost create portal)
mysql -u dbuser -p DBuser123! -h localhost portal < portal.sql

mysql -hhostname -uusername -ppassword databasename < backupfile.sql


还原压缩的MySQL数据库
gunzip < backupfile.sql.gz | mysql -uusername -ppassword databasename
```

##### 查看允许状态

```
观察state和info两列，查看有哪些线程在运行。经过查询发现之前远程删除的时候由于网络中断，锁表了.
mysql>show full processlist;
这时候只要使用kill命令+对应线程前面id，将线程结束掉，就可以正常删除了。
```

