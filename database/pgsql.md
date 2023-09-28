##### 数据导入导出命令

```shell
#导出数据
pg_dump -h 127.0.0.1 -U root -d thingsboard_gloabal -F c -f backup.dump
-h localhost: 指定数据库服务器的主机名或 IP 地址。
-U myuser: 指定连接数据库时使用的用户名。
-d mytimescaledb: 指定要导出的 TimescaleDB 数据库的名称。
-F c: 指定备份文件的格式为自定义格式（可选，但通常更适合大型数据库）。
-f timescaledb_backup.dump: 指定备份文件的输出路径和名称。
#恢复数据(注意这里没有使用-c: 在导入过程中清除（删除）已存在的目标数据库对象,在有timescaledb插件的情形下会恢复失败)
pg_restore -h 127.0.0.1 -U root -d thingsboard -F c backup.dump
-h localhost: 指定数据库服务器的主机名或 IP 地址。
-U myuser: 指定连接数据库时使用的用户名。
-d mytimescaledb: 指定要将备份数据导入的目标 TimescaleDB 数据库的名称。
-c: 在导入过程中清除（删除）已存在的目标数据库对象。
-F c: 指定备份文件的格式为自定义格式（如果备份文件是由 pg_dump 创建的）。

#拷贝容器文件到宿主机
docker cp postgresql:/home/postgres/backup.dump /Users/xiaozhong/Downloads
#拷贝宿主机文件到容器
docker cp backup.dump postgresql:/home/postgres
```

