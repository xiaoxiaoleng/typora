##### 中文学习地址

#####  https://flink.apache.org/zh/usecases.html

##### 初识flink

https://flink.apache.org/zh/flink-architecture.html

##### 入门指南

https://ci.apache.org/projects/flink/flink-docs-release-1.10/zh/getting-started/index.html



# Streaming Connectors

##### RabbitMQ Connector

https://ci.apache.org/projects/flink/flink-docs-release-1.2/dev/connectors/rabbitmq.html



##### TableApi

```
mvn archetype:generate \
    -DarchetypeGroupId=org.apache.flink \
    -DarchetypeArtifactId=flink-walkthrough-table-java \
    -DarchetypeVersion=1.10.0 \
    -DgroupId=spend-report \
    -DartifactId=spend-report \
    -Dversion=0.1 \
    -Dpackage=spendreport \
    -DinteractiveMode=false
```

##### 使用示例

https://ci.apache.org/projects/flink/flink-docs-release-1.10/zh/getting-started/examples/

##### 网上示例

- [如何使用 Apache Flink 构建有状态流数据应用](https://www.infoworld.com/article/3293426/big-data/how-to-build-stateful-streaming-applications-with-apache-flink.html)，这篇博客提供了基于 DataStream API 以及两个用于流数据分析的 SQL 查询实现的事件驱动的应用程序。
- [使用 Apache Flink、Elasticsearch 和 Kibana 构建实时仪表板应用程序](https://www.elastic.co/blog/building-real-time-dashboard-applications-with-apache-flink-elasticsearch-and-kibana)是 Elastic 的一篇博客，它向我们提供了一种基于 Apache Flink、Elasticsearch 和 Kibana 构建流数据分析实时仪表板的解决方案。
- 来自 Ververica 的 [Flink 学习网站](https://training.ververica.com/)也有许多示例。你可以从中选取能够亲自实践的部分，并加以练习。