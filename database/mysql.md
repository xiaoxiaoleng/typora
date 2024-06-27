##### 安装mysql

```shell
#检查是否已经安装过mysql
rpm -qa | grep mysql 
#如果安装，则删除
rpm -e --nodeps mysql-libs-版本 
#检查mysql用户组和用户，没有则创建
cat /etc/group | grep mysql
cat /etc/passwd |grep mysql
groupadd mysql
useradd -r -g mysql mysql
#下载mysql包（可到官网寻找其他版本）
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
#解压
tar xzvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
#解压完成后，移动该文件到/usr/local/mysql
mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
#创建data目录
mkdir /usr/local/mysql/data
#更改mysql目录下所有的目录及文件夹所属的用户组和用户，以及权限
chown -R mysql:mysql /usr/local/mysql
chmod -R 755 /usr/local/mysql
#编译安装并初始化mysql,务必记住初始化输出日志末尾的密码（数据库管理员临时密码root@localhost:后的字符串）
cd /usr/local/mysql/bin
./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
#启动mysql服务器
/usr/local/mysql/support-files/mysql.server start
如果出现如下提示信息
Starting MySQL.Logging to '/usr/local/mysql/data/iZge8dpnu9w2d6Z.err'.
#查询服务
ps -ef|grep mysql
ps -ef|grep mysqld
#结束进程
kill -9 PID
#添加软连接，并重启mysql服务
ln -s /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql 
ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
service mysql restart
#登录mysql,密码为刚才的临时密码
mysql -u root -p
#首先安装后，执行任何指令都会提示：
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
#将临时密码修改
>ALTER USER 'root'@'localhost' IDENTIFIED BY '123456' PASSWORD EXPIRE NEVER;
#刷新权限
>flush privileges;
#开放远程连接
>use mysql;
>update user set user.Host='%' where user.User='root';
>flush privileges;
#设置开机自启
#1、将服务文件拷贝到init.d下，并重命名为mysql
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
#2、赋予可执行权限
chmod +x /etc/init.d/mysqld
#3、添加服务
chkconfig --add mysqld
```





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

mysqlbinlog --stop-position=450579 /var/lib/mysql/mysql-bin.000017 -d  isc_ecology_form --skip-gtids | mysql -u root -p

--exclude-gtids
--include-gtid
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

##### 查看进程

```
show full processlist;

杀死进程
kill query 293804;

show variables like '%max_connection%';
show variables like 'wait_timeout%'
show status like 'Threads%';
show full processlist
-- set gloable wait_timeout
-- set GLOBAL wait_timeout = 600;
-- select id, db, user, host, command, time, state, info from information_schema.processlist order by time desc
```

##### sql注入扫描

##### 工具下载 

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

#####使用文档 

#####https://www.jianshu.com/p/65d7522ecc1f

https://sqlmap.kvko.live/usage

```python
python sqlmap.py  –dbms mysql --dbs --level 5 --cookie=token: c06db763-7c09-4b07-9164-179f77ebcd29 -u "https://open.isyscore.com/api/opencms/search?current=1&size=10&keyword=%271%27=%271%27&category=%E6%96%87%E7%AB%A0"
```

##### 删除所有表

```sql
select concat("DROP TABLE IF EXISTS ", table_name, ";") from information_schema.tables where table_schema="isc_function";
```

##### 案例

```python
# 假设http请求保存在如下文件中（http.txt):
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: no-cache
Connection: keep-alive
Cookie: BAIewD=E47869416FeqCBD231ED9C1ewqeF83C:FG=1; BIDUPSID=E47869416F8BE5026CBD231ED9C1F83C; PSTM=w1526868067; BDORZ=B490B5EBF6Fsaf3CD402E5eqDA1598; H_PS_PSSID=1437_21120_20928; PSINO=3
Host: baidu.com
Pragma: no-cache
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.181 Safari/537.36
#则使用如【下命令让sqlmap解析该文件，以该文件中HTTP请求目标为攻击目标进行测试：
#--batch使用默认配置
python sqlmap.py -r opencms_search.txt --level=5 risk=3 --batch
```

```python
#keyword=1
python sqlmap.py -u https://open.isyscore.com/api/opencms/search\?current\=1\&size\=10\&keyword\=%27\&category\=%E6%96%87%E7%AB%A0
-- 
python sqlmap.py -u https://open.isyscore.com/api/opencms/search\?current\=1\&size\=10\&keyword\=%27\&category\=%E6%96%87%E7%AB%A0 --dbs
```

##### 如何发现可注入的点

https://cloud.tencent.com/developer/article/1196791

```
可能的SQL注入点一般存在于登录页面、查找页面或添加页面等用户可以查找或修改数据的地方
```

##### BeanUtils.copyProperties(modelFieldParam, modelField);

```
保存数据时如果方法中使用了BeanUtils.copyProperties(modelFieldParam, modelField);copy对象中的id导致主键冲突产生注入风险
```

##### 按条件导出特定表

```mysql
mysqldump -uroot -pZljIsysc0re123 isc_ecology_form sys_dict_detail --where=" update_time>'2021-12-04 15:13:52'" > ./temp.sql
```

##### 命令模式下直接执行sql

```mysql
mysql>source ./temp.sql
```

##### postgresql查看用户连接及杀死会话

```sql
#SELECT pg_terminate_backend(pid)
#FROM pg_stat_activity
#WHERE datname = 'thingsboard';
#DROP DATABASE thingsboard;
select * from pg_stat_activity;
select * from pg_stat_activity WHERE datname = 'thingsboard';
#杀死thingsboard链接会话
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'thingsboard'
  AND pid <> pg_backend_pid();

```

