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
        //2、 查看索引
//        db[name + ".files"].getIndexes().forEach(function(item) {
//		 printjson("已存在索引 = " + item.name);
//		});
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

db.bucket.getIndexes();
db.file.getIndexes();
db.directory.getIndexes();
db.common.getIndexes();

 db["2fp75v6s.files"].createIndex( { 'filename': 1 } );
 db["2fp75v6s.files"].createIndex( { 'metadata': 1 });
 db["2fp75v6s.files"].createIndex( { 'metadata': 1,'filename': 1 });
 db["2fp75v6s.files"].createIndex( { 'metadata.dirId': 1,'filename': 1 });
 db["2fp75v6s.files"].createIndex({ 'metadata.uuid': 1, 'metadata.dirId': 1, 'metadata.isShare': 1});
 
 db["2fp75v6s.files"].getIndexes().forEach(function(item) {
		 printjson("已生成索引 = " + item.name);
		   //删除指定索引
 		//db["2fp75v6s.files"].dropIndex("filename_1")
  });

 db["2fp75v6s.files"].find({"filename" : "test.png"}).explain("executionStats")
 db["2fp75v6s.files"].find({"filename" : "a.gif"}).explain("executionStats")
 db["2fp75v6s.files"].find( { 
  "metadata" : {
        "dirId" : "b1837800b8f741ee87f73042b4d9f4a2"
    }
}).explain("executionStats")
 //删除全部索引
 db["2fp75v6s.files"].dropIndexes()

```



##### 注意事项

```javascript
//'metadata.dirId': 1,此写法会导致索引无效
db["2fp75v6s.files"].createIndex( { 'metadata.dirId': 1,'filename': 1 });
//最外层直接创建即可（'metadata': 1）
db["2fp75v6s.files"].createIndex( { 'metadata': 1,'filename': 1 });
```









