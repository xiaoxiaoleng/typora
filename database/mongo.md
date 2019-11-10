### mongo 索引

```javascript
(function () {
	db.bucket.createIndex({'tenantId': 1,'name': 1});   
	db.file.createIndex({'dirId': 1,'fileName': 1}); 
	db.directory.createIndex({'tenantId': 1,'absolutePath': 1}); 
	db.common.createIndex({'tenantId': 1,'fileName': 1,'path':1}); 
  	//gridfs创建索引
    db.bucket.find({}).forEach(function (bucket) {
        var name = bucket.name;
          printjson("bucket name = " + bucket.name);
        //1、创建索引
         db[name + ".files"].createIndex({'metadata': 1,'filename': 1});          
        //count为零删除对应GridFS及bucket中的记录
        var count = db[name + ".files"].find({}).count();
        if (count === 0) {
            printjson("删除 files、chunks: " + name);
             db[name + ".files"].drop();
             db[name + ".chunks"].drop();
             db.bucket.remove({"tenantId": bucket.tenantId, "name": bucket.name});
        }
    });
})();

 db["2fp75v6s.files"].getIndexes().forEach(function(item) {
		 printjson("已生成索引 = " + item.name);
		   //删除指定索引
 		db["2fp75v6s.files"].dropIndex( item.name)
  });

 db["2fp75v6s.files"].find({"filename" : "a.gif"}).explain("executionStats")
 db["2fp75v6s.files"].find( { 
  "metadata" : {
        "dirId" : "b1837800b8f741ee87f73042b4d9f4a2"
    }
}).explain("executionStats")


```

##### 注意事项

```javascript
//'metadata.dirId': 1,此写法会导致索引无效
db["2fp75v6s.files"].createIndex( { 'metadata.dirId': 1,'filename': 1 });
//最外层直接创建即可（'metadata': 1）
db["2fp75v6s.files"].createIndex( { 'metadata': 1,'filename': 1 });
```

##### 查询分析

```javascript
db["2fp75v6s.files"].find({"filename" : "test.png"}).explain("executionStats")
```

##### 获取索引

```
db.common.getIndexes();
```

##### 删除指定索引

```javascript
db["2fp75v6s.files"].dropIndex("filename_1")
```

##### 删除指定集合下全部索引

```javascript
//删除全部索引
db["2fp75v6s.files"].dropIndexes()
```

### mongo 操作日志分析

##### 日志分析

```javascript
use local
db.oplog.rs.stats().maxSize

db['oplog.rs'].find({"op" : "d","ns" : "file-storage.4zv0cjvr.files"})
db['oplog.rs'].find({ts:{$gte:Timestamp(1573357800,1)}, ts:{$lt:Timestamp(1573358100,1)},"ns" : "file-storage.4zv0cjvr.files"})

ts 的值： 表示该日志的时间戳
op 的值： i 表示 insert ，u 表示 update, d 表示 delete， c 表示的是 db cmd, db 表示声明当前数据库 (其中ns 被设置成为=>数据库名称+ '.')， n 表示 noop,,即空操作，其会定期执行以确保时效性
ns 的值： 表示操作所在的数据库和集合。
ui 的值： 表示当前登录用户的会话 id 值。
wall 的值: 表示该操作的执行时间，utc时间。
o 的值： 表示操作的内容，如果是插入，就会将插入的数据放到该位置。示例日志就是插入了一条数据{"name":"youju"}

```

### Oplog 恢复数据

##### 将数据库local中集合oplog.rs 导出

```shell
#按时间段导出
#导出2019-11-07 00:00:00 Timestamp(1573056000000,1)之后的操作记录
 ./mongodump --host 127.0.0.1 --port 27017 --authenticationDatabase admin --username root --password Root_123 -d local -c oplog.rs  --query '{ts:{$gte:Timestamp(1573056000000,1)}}' -o /data/mongodb_back/mongotestoplog
#导出2019-11-07 10:00:00 到2019-11-07 18:00:00时间段操作记录 
./mongodump --host 10.1.50.15 --port 27017 --authenticationDatabase admin --username root --password Root_123 -d local -c oplog.rs  --query '{ts:{$gte:Timestamp(1573092000000,1)}, ts:{$lt:Timestamp(1573120800000,1)}}' -o /data/mongodb_back/mongotestoplog
#按照操作、库备份
./mongodump --host 127.0.0.1 --port 27017 --authenticationDatabase admin --username root --password Root_123 -d local -c oplog.rs  --query '{"op" : "d","ns" : "file-storage.4zv0cjvr.files"}' -o /data/mongodb_back/mongotestoplog
#按照时间库备份
./mongodump --host 10.1.50.15 --port 27017 --authenticationDatabase admin --username root --password Root_123 -d local -c oplog.rs  --query '{ts:{$gte:Timestamp(1573265400,1)},"ns" : "file-storage.4zv0cjvr.files"}' -o /data/mongodb_back/mongotestoplog
#按照时间段库备份
./mongodump --host 10.1.50.15 --port 27017 --authenticationDatabase admin --username root --password Root_123 -d local -c oplog.rs  --query '{ts:{$gte:Timestamp(1573357800,1)}, ts:{$lt:Timestamp(1573358100,1)},"ns" : "file-storage.4zv0cjvr.files"}' -o /data/mongodb_back/mongotestoplog

#全量导出
./mongodump --host 10.1.50.15 --port 27017 --authenticationDatabase admin --username root --password Root_123 -d local -c oplog.rs -o /data/mongodb_back/mongotestoplog
```

#####执行还原命令

```shell
#oplog.rs 集合中导出数据进行还原
./mongodump --host 127.0.0.1 --port 27017 --oplogReplay --authenticationDatabase admin --username root --password Root_123 /data/mongodb_back/mongotestdump
```

##### 通过--oplogLimit 参数指定时间点

```shell
./mongorestore --host 127.0.0.1 --port 27017 --authenticationDatabase admin --username root --password root  --oplogReplay --oplogLimit "1573179000"   /data/mongodb_back/mongotestdump
```

##### GridFS 无法通过上述方式备份

```javascript
#GridFS 无法通过上述方式备份
```

##### 创建角色并授权

```javascript
db.createRole({role:'sysadmin',roles:[], privileges:[ {resource:{anyResource:true},actions:['anyAction']}]});
db.grantRolesToUser( "root" , [ { role: "userAdminAnyDatabase", db: "admin" } ]);

db.createUser(
  {
    user: "root",
    pwd: "root",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```



