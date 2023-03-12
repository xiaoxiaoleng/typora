###### 地址

http://54.180.78.21:5601/app/discover#/?_g=(filters:!(),refreshInterval:(pause:!t,value:0),time:(from:now-15m,to:now))&_a=(columns:!(_source),filters:!(),index:'7e2d7ec0-5e1f-11eb-83e3-81cf1b7103a3',interval:auto,query:(language:kuery,query:''),sort:!())

##### 查看索引

```shell
#确定集群状态
curl 'http://localhost:9200/_cluster/health?pretty'
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
#删除uni-paycenter一周前的索引
curl -X POST "http://localhost:9200/uni-paycenter/_delete_by_query?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
        "range": {
            "@timestamp": {
                "lt": "now-7d",
                "format": "epoch_millis"
            }
        }
    }
}'

curl -XPUT 'localhost:9200/uni-paycenter?pretty'
curl 'http://localhost:9200/_cat/indices?v'
curl 'http://localhost:9200/_cat/indices?v'
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

## 删除所有索引
curl -X DELETE 'http://localhost:9200/_all'
# 原理就是先执行 _cat/indices 列出所有的索引，然后循环结果，一个一个删除
for i in `curl -XGET 'http://localhost:9200/_cat/indices' | awk '{print $3}'`; do curl -XDELETE "http://localhost:9200/$i"; done

#-u是格式为userName:password，使用Basic Auth进行登录。如果elasticsearch没有使用类似x-pack进行安全登录，则不需要加-u参数
curl -u 用户名:密码  -H'Content-Type:application/json' -d'{
    "query": {
        "range": {
            "@timestamp": {
                "lt": "now-7d",
                "format": "epoch_millis"
            }
        }
    }
}
' -XPOST "http://127.0.0.1:9200/*-*/_delete_by_query?pretty"

#每天0点删除超过7天的无效索引
crontab -e
* 0 * * * /usr/bin/curl -u username:password  -H'Content-Type:application/json' -d'{"query":{"range":{"@timestamp":{"lt":"now-7d","format":"epoch_millis"}}}}' -XPOST "http://127.0.0.1:9200/*-*/_delete_by_query?pretty" > /tmp/elk_clean.txt

```

##### elk修复

```shell
/opt/elk/elasticsearch-7.9.3/bin
ps aux | grep elasticsearch

sudo adduser es
#设置密码
sudo passwd es

sudo chown -R es:es /opt/elk/elasticsearch-7.9.3/bin/elasticsearch

sudo chmod -R 777  /path/to/data/nodes 

sh elasticsearch-7.2.0/bin/elasticsearch -d
# 后台启动 重启(重启不一定会成功)
sh /opt/elk/elasticsearch-7.9.3/bin/elasticsearch -d

ps aux|grep logstash

nohup /opt/elk/logstash-7.9.3/bin/logstash -f /opt/elk/logstash-7.9.3/config/logstash-sample.conf &

netstat -tunlp|grep 5601

sh /opt/elk/kibana-7.9.3-linux-x86_64/bin/kibana

nohup /opt/elk/kibana-7.9.3-linux-x86_64/bin/kibana &


nohup /opt/elk/kibana-7.9.3-linux-x86_64/bin/kibana > /dev/null 2>&1 &

curl -XPUT -H 'Content-Type: application/json' "http://127.0.0.1:9200/_cluster/settings" -d '
{
	"transient" : {
	"cluster.routing.allocation.enable" : "all"
	}
}'
```

