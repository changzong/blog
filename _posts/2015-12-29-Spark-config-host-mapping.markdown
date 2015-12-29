---
layout: post
title:  "Spark和Hadoop在Mac上的配置以及一个地址映射问题"
date:   2015-12-29 11:02:00
categories: jekyll update
---

在Mac上配置Hadoop和Spark的步骤可以参考下面链接的教程，比较详细靠谱：

[hadoop2.7+Spark1.4环境搭建][hadoop&spark]

我用的版本是hadoop2.7.1和spark1.5.2以及java1.8.0

需要注意的问题有：

* 在重新格式化节点和启动hadoop的时候，会出现datanode不存在的情况，这时候需要用

> $ sbin/stop-dfs.sh

停止hadoop后再删除/tmp/hadoop-xxx这个文件夹：

> $ rm -rf /tmp/hadoop-xxx

然后重新格式化和启动：

> $ bin/hdfs namenode -format

> $ sbin/start-dfs.sh

* 创建hdfs路径的时候一定要用 /user :

> $ bin/hdfs dfs -mkdir /user

> $ bin/hdfs dfs -mkdir /user/changPC

* Spark的配置要在conf/spark-env.sh中添加下面三条：

> export HADOOP_HOME=/usr/local/hadoop-2.7.1

> export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

> export SPARK_DIST_CLASSPATH=$(hadoop classpath)

hadoop的环境变量要添加进PATH中

* 在启动spark/bin/pyspark时出现socket.py返回的nodename unknown错误

经查找需要在Mac系统下/etc/hosts中修改，将

> 127.0.0.1       windcandle.local

改为

> 127.0.0.1       windcandle.local localhost

添加地址映射，之前需要chmod修改文件权限

* 次修改之后在pyspark中执行spark程序时会出现could not open socket错误

这个错误的原因有很多，网上针对spark的解答只有将java版本调低，但是最新的spark版本已经可以支持java8了。

我对错误来源的spark代码研究了一番，肯定是地址和端口无法通过socket打开，而端口每次都是变化的，可见是spark随机分配的worker端口，所以将目标锁定在了host地址。联想到了之前对/etc/hosts文件的修改，于是做出以下决定。

需要在spark源代码中做修改，在spark/python/pyspark/rdd.py中修改_load_from_socket函数中

> localhost

改为

> 127.0.0.1

即可

[hadoop&spark]: http://my.oschina.net/laigous/blog/478438