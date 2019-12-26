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

```
./mysqlbinlog --stop-position=9934919 -d portal --skip-gtids /opt/dbdata/mysql/mysql_bin.000002 > portal.sql
```

##### 4、查看二进制日志文件（解码）

```
./mysqlbinlog --no-defaults --base64-output=decode-rows /opt/dbdata/mysql/mysql_bin.000002 > bak.txt
```

##### 5、将数据恢复到时间点

```
./mysql -u dbuser -p -e "source ./portal.sql"
```

```
./mysqlbinlog --stop-datetime="2019-12-23 15:30:34" -d portal --skip-gtids /opt/dbdata/mysql/mysql_bin.000002 | mysql -udbuser -p
```

##### 注意事项

```
进行时间点备份之前 先做全量备份
```

##### 备份所有数据库

```
./mysqldump -udbuser -pDBuser123! --all-databases > all_database.sql
```

##### 备份单个数据库的所有表

```
./mysqldump -udbuser -pDBuser123! --set-gtid-purged=off --databases portal>portal_bak.sql
```



##### 查看mysqld工作目录

```
ps -ef | grep defaults | grep 3306 | awk '{print $8}'
```

##### 判断是否为主节点

```
show master status;

show slave status;
```



#### 输出结果展示

```
使用 \G , 比较推荐这个用法，把行转化成列显示
show slave status \G
查询语句使用 \G 结尾，不需要 ;了。推荐经常用这种写法，很实用。
```

