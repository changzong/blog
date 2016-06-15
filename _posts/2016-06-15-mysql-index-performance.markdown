---
layout: post
title:  "MySQL索引优化最左前缀原则理解"
date:   2016-06-15 08:47:00
categories: jekyll update
---

最近读了美团的一篇优化MySQL慢查询的文章，结合自己的理解，来做一下联合索引的最左前缀匹配原则备忘。

美团博文：

* [*MySQL索引原理及慢查询优化*](http://tech.meituan.com/mysql-index.html)

另一篇不错的优化博客：

* [*MySQL查询优化-explain*](http://my.oschina.net/wcsm520/blog/222694)

在建立联合索引的时候，根据查询过程，MySQL会从左到右依次检查联合索引中的各个索引，看它在查询语句中的作用，一旦遇到范围查询而非值查询的时候就停止匹配了，之后的其他索引就无法被使用。

比如：

我们的查询语句中包含有 

```
where a=1 and b=2 and c>3 and d=4
```

而我们建立联合索引为(a,b,c,d)，则索引d不会被使用，从而整个索引失效。如果我们改为(a,b,d,c)，则整个索引会被使用。其中a,b,d的顺序可以变化。我们也可以只建立(a,b,d)这样的所以来避开范围查询c。

索引被不被使用可以通过explain查询来获得，结果中的Extra字段里如果包含*Using filesort*内容，则索引失效，需要进行优化。