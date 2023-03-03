###### 地址

http://54.180.78.21:5601/app/discover#/?_g=(filters:!(),refreshInterval:(pause:!t,value:0),time:(from:now-15m,to:now))&_a=(columns:!(_source),filters:!(),index:'7e2d7ec0-5e1f-11eb-83e3-81cf1b7103a3',interval:auto,query:(language:kuery,query:''),sort:!())



##### 查看索引

```shell
#确定集群状态
curl http://*:9200/_cluster/health?pretty
#查看所有索引状态
curl 'http://localhost:9200/_cat/indices?v'
# 查看异常索引状态
curl -XGET "http://localhost:9200/_cat/indices?v&health=red"
#查看异常索引分片分配状态
curl -XGET "http://localhost:9200/_cat/shards/some_index_name?v"
curl -XGET "http://localhost:9200/_cat/shards/uni-paycenter?v"
#查看分片分配不成功的原因
curl -XGET "http://localhost:9200/_cat/shards/some_index_name?v&h=n,index,shard,prirep,state,sto,sc,unassigned.reason,unassigned.details"
#重新分配失败的分片
url -XPOST "http://localhost:9200/_cluster/reroute?retry_failed=true"
#调整ES进行分片分配的重试次数（默认为5次）
curl -XPUT "http://localhost:9200/some_index_name/_settings" -d'
{
  "index": {
    "allocation": {
      "max_retries": 20
    }
  }
}'
#删除索引
curl -XDELETE 'http://localhost:9200/.kibana_task_manager_1?pretty'
curl -XDELETE 'http://localhost:9200/uni-paycenter?pretty'
curl -XDELETE 'http://localhost:9200/.kibana_task_manager_1?pretty'
curl -XDELETE 'http://localhost:9200/uni-paycenter?pretty'
curl -XPUT 'localhost:9200/uni-paycenter?pretty'
curl 'http://localhost:9200/_cat/indices?v'
curl 'localhost:9200/_cat/indices?v'
curl http://localhost:9200/_cluster/health?pretty
curl 'http://localhost:9200/_cat/indices?v' |grep uni-paycenter
#创建索引
curl -XPOST -H 'Content-Type: application/json' 'localhost:9200/uni-paycenter/_doc/1?pretty' -d '
{
  "name": "uni-paycenter"
}'
#更新索引
curl -XPUT -H 'Content-Type: application/json' 'localhost:9200/uni-paycenter/_doc/1?pretty' -d '
{
  "name": "uni-paycenter"
}'
curl -XPUT 'http://localhost:9200/_cluster/settings' -d '
{
    "transient" : {
        "cluster.routing.allocation.node_concurrent_recoveries": "20",
        "indices.recovery.max_bytes_per_sec": "100mb"
    }
}'
curl -XGET  'http://localhost:9200/_cat/indices?v&health=red'
#查看索引信息
curl -XGET 'http://localhost:9200/uni-paycenter?pretty'
curl -k -XGET 'http://127.0.0.1:9200/_cluster/allocation/explain?pretty'

```



