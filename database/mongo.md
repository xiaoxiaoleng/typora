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

##### 开启慢查询

```javascript
db.getProfilingStatus() 
#查看级别
db.getProfilingLevel() 
#设置级别和时间
db.setProfilingLevel(2,200)
# 关闭
db.setProfilingLevel(0)
```



##### 慢查询

```javascript
#查询运行时间大于500毫秒的语句
db.system.profile.find( { millis : { $gt : 500 } } )
#查询最近的语句
db.system.profile.find().sort({$natural:-1})
#查询一段时间内的语句
db.system.profile.find({ts:{$gt:new ISODate("2012-07-19T03:00:00Z"),$lt:new ISODate("2012-07-19T03:40:00Z")}})
```

##### 认证登录

```shell
./mongo --host 10.1.50.15 --port 27017 -uroot -pRoot_123 --authenticationDatabase admin
```

##### 判定当前节点是否为主节点

```
db.isMaster()
--------------
rs.status()
```

##### 备份数据库

```
./mongodump --host 10.1.50.15 --port 27017 -umongo -pMongoDB_863*^# --authenticationDatabase admin -d pacific -o /opt/mongobackup

./mongodump --host 10.1.50.15 --port 27017 -umongo -pMongoDB_863*^# --authenticationDatabase admin -d cmdb -o /opt/mongobackup
```

##### 还原数据库

```
./mongorestore -h 10.1.50.15 --port 27017 -umongo -pMongoDB_863*^# --authenticationDatabase admin -d pacific /opt/mongobackup/pacific

./mongorestore -h 10.1.50.15 --port 27017 -umongo -pMongoDB_863*^# --authenticationDatabase admin -d pacific /opt/mongobackup/cmdb


./mongorestore -h 10.1.50.15 --port 27017 -umongo -pMongoDB_863*^# --authenticationDatabase admin -d cmdb /opt/mongobackup/central_plains/cmdb

./mongorestore -h 10.1.50.15 --port 27017 -umongo -pMongoDB_863*^# --authenticationDatabase admin -d pacific /opt/mongobackup/central_plains/pacific
```

##### 扩展字段(incre_id)

```javascript
(function () {
    var id = 0;
    db.bucket.find({}).forEach(function (item) {
        id=id+1;
        item.incre_id = id;
        db.bucket.save(item);
    }); 
})();

(function () {
    var id = 0;
    db.resObject.find({"attrValues.lifecycleState":{ $exists: false}}).forEach(function (item) {
         var lifecycle={};
         lifecycle.V="using";
         lifecycle.T="U";
        item.attrValues.lifecycleState = lifecycle;
        item.append_state=true;
        db.resObject.save(item);
    }); 
})();
```

##### 删除字段(incre_id)

```javascript
db.bucket.update({},{$unset: {incre_id:1}},{multi: true});
db.resObject.update({"append_state" : true},{$unset: {"attrValues.lifecycleState":""}},{multi: true});
```

##### 获取最大最小值

```js
db.collection.find().sort({age:-1}).limit(1) // for MAX
db.collection.find().sort({age:+1}).limit(1) // for MIN
db.resObject.find( { "attrValues.lifecycleState" : { $exists: true } }).sort({createTime:+1})
```

##### query nested conditions

```javascript
db.inventory.insertMany( [
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);

db.inventory.find( { size: { h: 14, w: 21, uom: "cm" } } )
db.inventory.find(  { size: { w: 21, h: 14, uom: "cm" } }  )
db.inventory.find( { "size.h": { $lt: 15 } } )

```

#####存在字段（lifecycleState）

```javascript
db.resObject.find( { "attrValues.lifecycleState" : { $exists: true } } ).count()
```

##### And查询

```javascript
db.resObject.find( { $and: [ { "attrValues.lifecycleState" : { $exists: true } }, { "createTime" : { "$gte" : ISODate("2018-01-11T09:34:55.556Z"), "$lt" : ISODate("2019-12-16T07:28:17.344+0000") } } ] } )

db.resHistory.find({ $and: [{ "tenantId" : "e10adc3949ba59abbe56e057f20f88dd" },  { "res.attrValues.lifecycleState" : { $exists: true } }, { "res.createTime" : { "$gte" : ISODate("2018-01-11T09:34:55.556Z"), "$lt" : ISODate("2019-12-16T07:28:17.344+0000") } } ] })


```

##### 从secondary节点备份数据

https://docs.mongodb.com/manual/reference/program/mongodump/

```html

```

##### 强制修改当前节点为主节点

https://docs.mongodb.com/manual/tutorial/force-member-to-be-primary/

```

```

##### 从存活的副本集重新选举主节点

https://docs.mongodb.com/manual/tutorial/reconfigure-replica-set-with-unavailable-members/

```

```

##### 停止mongod服务

https://docs.mongodb.com/manual/tutorial/manage-mongodb-processes/

```
use admin
db.shutdownServer()
----------
kill <mongod process ID>
kill -2 <mongod process ID>
```

##### mongodb被锁定后 --repair

https://mongodb-documentation.readthedocs.io/en/latest/tutorial/recover-data-following-unexpected-shutdown.html

```
 1）首先删除/var/lib/mongo/目录下的mongod.lock文件
 rm /var/lib/mongo/mongod.lock
 2) repair方式启动mongodb
 mongod -f /etc/mongod.conf --repair
 3) 再启动一次mongodb
 这里一定要再启动一次，不然启动client端仍然连不到server
 mongod -f /etc/mongod.conf

也可以重新指定一个repairPath

mongod --dbpath /etc/mongo/db --repair --repairPath /etc/mongo/db0

mongod --dbpath /etc/mongo/db --repair --repairPath /etc/mongo/db0

查了查mongodb的文档，遂用以下方法进程修复：
 
 首先停止mongod服务，删除 mongodb.log，也可以备份一下
 # rm -rf  /data/mongodb/mongodb.log
 
 删除mongodb进程文件
 # rm -rf  /mongodb/mongod.lock
 
 进行修复

# /usr/local/mongodb/bin/mongod --repair --dbpath /mongodb/ --repairpath /mongodb/repair/

如果后台执行

./mongod --repair --dbpath /data/dbdata --repairpath /data/repair/ --logpath /data/dblog/mongodbrepair.log --fork
```

##### Enable Access Control

https://docs.mongodb.com/manual/tutorial/enable-authentication/

##### 仅查询需返回的列("_id":1,"attrValues.lifecycleState":1)

```
db.getCollection("resObject").find({"$and":[{"attrValues.lifecycleState":{"$exists":"true"}}]},{"_id":1,"attrValues.lifecycleState":1})
```

##### 组合索引

```javascript
db.person.save({ 
    "_id" : ObjectId("61d538b48efd0f502c36a196"), 
    "firstname" : "123", 
    "lastname" : "456", 
    "_class" : "com.isyscore.ocean.pojo.Person"
});
db.person.createIndex({"firstname":-1,"lastname":-1});
//会走索引
db.person.find({"firstname":"8"}).explain("executionStats");
//全表扫描-索引不生效
db.person.find({"lastname":"8"}).explain("executionStats")
```

