---
layout: post
title:  "Kafka失效引发的Flume Agent堵塞使数据丢失的处理"
date:   2016-05-18 19:19:00
categories: jekyll update
---
之前配置的大数据流架构中的一种线路是这样的：


```
log server -> Kafka -> Flume -> HDFS 
```

同时

```
log server -> Kafka -> Flume -> Kafka(测试) -> Spark code -> Kafka(测试) -> LogStash -> ES
```

这两条线路前三步是相同的，但是第二步中的Kafka(测试)部分在运行的时候出现了错误，导致大量特定topic的数据堆积在Flume代理服务器上，无法被消费。进而耗尽了Flume服务器内存使其宕机，本来要写入到HDFS中的数据也一同消失了。

后来分析了原因，觉得Flume应该会有相应的处理机制，即能够将数据备份，或者能够在重启后重新读入之前失效前的数据。

查看了Flume的官方文档，发现Flume由三部分组成：Source，Channel，和Sink。正是由于Kafka(测试)这个Sink出错导致Flume所使用的Channel堵塞宕机，最后使Source Kafka的数据无法正确写入的其他Sink中，如HDFS。

那么[Flume][Flumedoc]使用了什么Channel导致的堵塞呢？原来它默认配置的是memory模式，即内容通道。进来的数据直接通过内存一条条转发给Kafka(测试)，一旦出口有问题了，Flume内存中会堆积越来越多的Source数据出不去，一直到将其内存耗尽，无法进行任何其他进程。这时只能重新启动，可是原本在内存中的数据就会丢失掉，从而造成损失。

Flume还提供了另外一些Channel选择，其中有一个叫SPILLABLEMEMORY，即内存和磁盘共存模式。当内存中的数据太多达到上限的时候，Flume会自动将数据引流到磁盘中去，你需要做的只是分别配置内存和磁盘的容量上限，以及写入磁盘文件的目录即可，配置如下：

```
tier1.channels.c.type = SPILLABLEMEMORY
tier1.channels.c.memoryCapacity = 10000
tier1.channels.c.overflowCapacity = 100000000
tier1.channels.c.byteCapacity = 800000
tier1.channels.c.checkpointDir = /mnt/flume/checkpoint
tier1.channels.c.dataDirs = /mnt/flume/data
```

Source和Sink的配置基本和原来相同，只是为了测试，新添加了一个Kafka Source来避免与生产Kafka冲突：

```
tier1.sources  = s s1
tier1.channels = c
tier1.sinks    = k

tier1.sources.s.type = org.apache.flume.source.kafka.KafkaSource
tier1.sources.s.zookeeperConnect = xx.xx.xx.xx:2181,xx.xx.xx.xx:2181,xx.xx.xx.xx:2181
tier1.sources.s.groupId = group1
tier1.sources.s.topic = originTopic
tier1.sources.s.batchSize = 1000
tier1.sources.s.channels = c

tier1.sources.s1.type = org.apache.flume.source.kafka.KafkaSource
tier1.sources.s1.zookeeperConnect = xx.xx.xx.xx:2181,xx.xx.xx.xx:2181,xx.xx.xx.xx:2181
tier1.sources.s1.groupId = groupTest
tier1.sources.s1.topic = testTest
tier1.sources.s1.batchSize = 1000
tier1.sources.s1.channels = c

tier1.sinks.k.type = hdfs
tier1.sinks.k.channel = c
tier1.sinks.k.hdfs.path = hdfs://xx.xx.xx.xx:8022/user/flume/%{topic}/%Y-%m-%d
tier1.sinks.k.hdfs.round = true
tier1.sinks.k.hdfs.filePrefix=%H
tier1.sinks.k.hdfs.fileSuffix =.log
tier1.sinks.k.hdfs.rollInterval=3600
tier1.sinks.k.hdfs.rollSize=133169152
tier1.sinks.k.hdfs.rollCount=0
tier1.sinks.k.hdfs.writeFormat=Text
tier1.sinks.k.hdfs.fileType=DataStream
tier1.sinks.k.hdfs.batchSize=100000
tier1.sinks.k.hdfs.serializer=avro_event
tier1.sinks.k.hdfs.threadsPoolSize=5
tier1.sinks.k.hdfs.idleTimeout=30
```

这样配置之后，即使测试的Kafka出问题，Flume消费不了一些topic的数据，它也不会导致内存耗尽而宕机，而是在数据量达到了内存的上限之后，将数据引流到服务器磁盘中保存下来。一旦Flume服务器遇到其他问题需要重启，启动后还会从磁盘中恢复保存下的数据，从而大大提升了容错性。

[Flumedoc]: http://flume.apache.org/FlumeUserGuide.html