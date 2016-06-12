---
layout: post
title: "Hive学习及有用查询语句整理"
date: 2016-05-12 08:34:00
categories: jekyll update
---
### 感受

通过一段时间的Hive学习，认为它的功能还是比较强大的。Hive可以满足比较复杂的HDFS取数工作，从而避免了使用其他编程语言如Python或Scala通过Spark接口读取HDFS数据在进行转换。Hive底层是转化为MapReduce程序来处理文件数据。同时提一下Hive的一个可视化平台Hue，在上面可以很方便地写查询语句并保存下来或者写成作业流让查询定期自动运行，此外，Hue还支持除了Hive以外的多种大数据处理作业，如Spark程序和Sqoop导出数据。此外，Hive拥有比较强大的集合操作，能够解决MySQL不能胜任的一些复杂运算。

### 例子

* 建表

```
CREATE TABLE `my_hive_table`(
`date` date COMMENT 'from deserializer', 
`partyid` string COMMENT 'from deserializer', 
`category` string COMMENT 'from deserializer', 
`frequency_sum` int COMMENT 'from deserializer',
`publish_midnight` boolean COMMENT 'from deserializer',
`party_name` string COMMENT 'from deserializer',
`mobile_number` string COMMENT 'from deserializer',
`party_type` string COMMENT 'from deserializer',
`city` string COMMENT 'from deserializer',
`trade_type` string COMMENT 'from deserializer')
```

* 删除表

```
drop table my_hive_table
```

* 查看表结构

```
show create table my_hive_table
```

* 简单取数

```
select count(*) from my_hive_table_1 where code=26 and partyid=1831893 and time>=date_add(current_date, -1) and time<current_date
```

* union all

```
select count(*) from 
(select * from my_hive_table_1 where code=26 and partyid=1831893 and time>=date_add(current_date, -1) and time<current_date
union all 
select * from my_hive_table_2 where code="26" and partyid=1831893 and time>=date_add(current_date, -1) and time<current_date) t
```

* 插入数据到表（文件）

```
insert into table logs.my_hive_table
select count(*) from my_hive_table_1 where code=26 and partyid=1831893 and time>=date_add(current_date, -1) and time<current_date
```

* 复杂查询（带左外联，排序，groupby，集合函数等）

```
select tmp3.*, other_table.party_name, other_table.mobile_number, other_table.party_type, other_table.city, other_table.trade_type from 
(select date, partyid, category, sum(frequency) as frequency_sum, array_contains(collect_set(publish_midnight),true) as publish_midnight from
(select time, partyid, serialnumber, mac, os, phonemode, code, uuid, address, goodssourceid, "发布货源" as category, 
   IF(time>=cast(concat(cast(to_date(date_add(current_date, -7)) as string), " 22:00:00") as timestamp) or 
      time<=cast(concat(cast(to_date(date_add(current_date, -7)) as string), " 06:00:00") as timestamp), true, false) as publish_midnight, 
to_date(time) as date, 1 as frequency 
from logs.my_hive_table_3 
where code=26 and time>=date_add(current_date, -7) and time<date_add(current_date, -6) and partyid is not NULL
union all
select time, partyid, serialnumber, mac, os, phonemode, code, uuid, address, goodssourceid, "发布货源" as category, 
   IF(time>=cast(concat(cast(to_date(date_add(current_date, -7)) as string), " 22:00:00") as timestamp) or 
      time<=cast(concat(cast(to_date(date_add(current_date, -7)) as string), " 06:00:00") as timestamp), true, false) as publish_midnight, 
to_date(time) as date, 1 as frequency  
from logs.my_hive_table_4 
where code="26" and time>=date_add(current_date, -7) and time<date_add(current_date, -6) and partyid is not NULL) tmp
group by date, partyid, category) tmp3 
left outer join mirror.other_table on (tmp3.partyid = other_table.party_id)
order by frequency_sum desc limit 100000
```
（注意其中的array_contains()和collect_set()函数）

* 导出Hive表至MySQL

```
CREATE TEMPORARY FUNCTION dboutput AS 'org.apache.hadoop.hive.contrib.genericudf.example.GenericUDFDBOutput';
select dboutput('jdbc:mysql://xx.xx.xx.xx:3306/db','username','password',
                'INSERT INTO MyMySQLTable(col1, col2, col3) VALUES (?,?,?)',
                hivecol1, hivecol2, hivecol3)
from logs.my_hive_table where date>=date_add(current_date, -1) and date<current_date 
```

* 给表添加属性防止错误json格式影响

```
ALTER TABLE logs.my_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");
```

* Hive的优化
一条Hive查询如下：

```
select date, "成交" as event, data['partyid'] as partyid, data['extra_uuid'] as uuid, data['goodssourceid'] as goodssourceid 
from logs.trade where data['__url'] = '/trade/tradecs/driverPreDeal' and date >= current_date
union all
select date, "成交" as event, data['partyid'] as partyid, data['extra_uuid'] as uuid, data['goodssourceid'] as goodssourceid 
from logs.trade where data['__url'] = '/trade/tradecs/ownerPreDeal' and date >= current_date
```

可优化为：

```
select date, "成交" as event, data['partyid'] as partyid, data['extra_uuid'] as uuid, data['goodssourceid'] as goodssourceid 
from logs.trade where
pt=current_date and
array_contains(array('/trade/tradecs/driverPreDeal20160428','/trade/tradecs/ownerPreDeal'), data['__url']);
```

解释：尽量在创建hive表的时候使用partitioned by参数来分区，之后查询使用分区字段来代替限制条件，避免全表扫描。尽量不要使用union all或or来做结果联合，使用hive函数array_contains来提高效率，减少扫描表的次数。
建立带分区的表可以：

```
CREATE EXTERNAL TABLE `trade`(
 `type` string COMMENT 'from deserializer', 
 `date` timestamp COMMENT 'from deserializer', 
 `key` string COMMENT 'from deserializer', 
 `data` map<string,string> COMMENT 'from deserializer')
 PARTITIONED BY ( 
 `pt` string)
 ROW FORMAT SERDE 
 'org.openx.data.jsonserde.JsonSerDe' 
 WITH SERDEPROPERTIES ( 
 'ignore.malformed.json'='true') 
 LOCATION 
 'hdfs://nameservice1/user/flume/trade'
```

然后需要手动添加每一个分区（写成Oozie作业每天执行）：

```
alter table log_trade add partition(pt='2016-06-04') location '2016-06-04'
```

其中location代表分区所对应的日志目录文件名。