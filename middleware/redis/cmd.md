

##### 对String操作的命令

```shell
set(key, value)：给数据库中名称为key的string赋予值value
get(key)：返回数据库中名称为key的string的value
getset(key, value)：给名称为key的string赋予上一次的value
mget(key1, key2,…, key N)：返回库中多个string（它们的名称为key1，key2…）的value
setnx(key, value)：如果不存在名称为key的string，则向库中添加string，名称为key，值为value
setex(key, time, value)：向库中添加string（名称为key，值为value）同时，设定过期时间time
mset(key1, value1, key2, value2,…key N, value N)：同时给多个string赋值，名称为key i的string赋值value i
msetnx(key1, value1, key2, value2,…key N, value N)：如果所有名称为key i的string都不存在，则向库中添加string，名称key i赋值为value i
incr(key)：名称为key的string增1操作
incrby(key, integer)：名称为key的string增加integer
decr(key)：名称为key的string减1操作
decrby(key, integer)：名称为key的string减少integer
append(key, value)：名称为key的string的值附加value
substr(key, start, end)：返回名称为key的string的value的子串
```



##### 对value操作相关命令

```scheme
exists(key)：确认一个key是否存在
del(key)：删除一个key
type(key)：返回值的类型
keys(pattern)：返回满足给定pattern的所有key
randomkey：随机返回key空间的一个key
rename(oldname, newname)：将key由oldname重命名为newname，若newname存在则删除newname表示的key
dbsize：返回当前数据库中key的数目
expire：设定一个key的活动时间（s）
ttl：获得一个key的活动时间
select(index)：按索引查询
move(key, dbindex)：将当前数据库中的key转移到有dbindex索引的数据库
flushdb：删除当前选择数据库中的所有key
flushall：删除所有数据库中的所有key
```

##### 查询

```
GET widgetCache:portal_cache_widgetd92f30dee46846ed8d0328e9e3b1807ee10adc3949ba59abbe56e057f20f88dde10adc3949ba59abbe56e057f20f88dd
```

##### 删除

```
DEL appCache:portal_cache_appe10adc3949ba59abbe56e057f20f88dde10adc3949ba59abbe56e057f20f88dd
```

##### 登陆redis客户端

```
$ redis-cli -h host -p port -a password
$redis-cli -h 10.1.62.147 -p 6379 -a "Redis@456_$%^"
```



##### 判断主从节点连接数

```
INFO
#最大连接数
CONFIG GET maxclients
获取客户端列表
CLIENT LIST
设置当前连接点redis的名称
CLIENT SETNAME    
查看当前连接的名称
CLIENT GETNAME    
杀死指定连接
CLIENT KILL ip:port    
```

##### 刷新

