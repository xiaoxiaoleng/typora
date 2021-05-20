###### Consuming big messages from Kafka

```
https://ibmstreams.github.io/streamsx.kafka/docs/user/ConsumingBigMessages/
```

##### 

##### 列出主题

```
./kafka-topics.sh --list --bootstrap-server localhost:9192


./kafka-topics.sh --list --zookeeper 10.1.60.114:2181
```

##### create topic

```
./kafka-topics.sh --create --bootstrap-server 10.1.60.124:9192 --replication-factor 1 --partitions 1 --topic test

./kafka-console-producer.sh --broker-list 10.1.60.124:9192 --producer.config /opt/kafka/kafka/config/producer.properties --topic test
```

##### 发送消息

```
./kafka-console-producer.sh --bootstrap-server 10.1.60.124:9192 --topic test This is a message
This is another message
```



##### 查看主题

```
./kafka-console-consumer.sh --bootstrap-server 10.1.60.124:9192 --topic uyun.ant.topic.gather-exec --from-beginning
```

```undefined
./kafka-topics.sh --describe --zookeeper 10.1.60.114:2181 --topic uyun.ant.topic.gather-exec
./kafka-console-consumer.sh --bootstrap-server 10.1.60.124:9192 --topic test --consumer.config /opt/kafka/kafka/config/consumer.properties --from-beginning

```

#####消费组列表

```
./kafka-consumer-groups.sh --new-consumer --bootstrap-server localhost:9192 --list
```

##### 消费组信息

```
./kafka-consumer-groups.sh --describe --bootstrap-server 10.1.60.124:9192 --group uyun.gather.consumer.package.script.exec.result

./kafka-consumer-groups.sh --describe --bootstrap-server 10.1.60.124:9192 --group uyun.gather.consumer.process.clean.task.result
```



##### 删除topic，慎用，只会删除zookeeper中的元数据，消息文件须手动删除

```
bin/kafka-run-class.sh kafka.admin.DeleteTopicCommand --zookeeper node01:2181 --topic t_cdr
```



#### 删除消费组

```
./kafka-consumer-groups.sh --bootstrap-server localhost:9192 --delete --group uyun.gather.topic.extract.transform.load.result --group uyun.gather.topic.extract.transform.load.result-local
```

