---
layout: post
title:  "基于数据Web应用敏捷开发"
date:   2015-12-08 09:21:00
categories: jekyll update
---
将数据从后台数据库通过Web展现出来，无论是对于面向外部的咨询类公司还是面向内部的开发型公司都越来越需要，以便能够更加直观地展现数据中包含的信息，方便客户或业务人员参与商业决策。对于这种时效性比较强的需求，快速地开发出令人耳目一新并且易上手的产品是满足要求的关键。

最近通过不到一周的时间，本人写出了一个简单的Web应用，其目的是将结构化数据从后台MySQL数据库中的某一表中提取出来，通过浏览器以比较美观的形式实时展现出来，并加入添加条目，修改或删除原有条目的功能，以便让数据库管理人员不用连接数据库和写SQL语句，通过鼠标简单的操作实现数据表格的管理。总的来说，就是使用后台编程技术接收前端POST请求访问数据库再将数据内容返回给前端，前端渲染界面的同时监听用户行为再将新的请求发送给后端，符合MVC的设计思路。

使用语言包括（由后至前）：

+ SQL（使用MySQL connector嵌入后端语言中）

+ Python（与数据库通信，处理数据，接收前端请求和发送数据至前端）

+ JavaScript（数据可视化逻辑设计，AJAX与后端通信，行为监听）

+ HTML（载体）

在Python中使用[MySQL Python connector][pymysql]与数据库进行通信，并使用开源且轻量级的[Flask][flask]作为后端开发库，快捷方便地建立小型服务器（之前用过Django开发过其他Web应用，相比而言，Flask更加小型并满足本项目的需求，上手极快，官方文档简洁易懂）。

作为数据可视化目前最流行的JavaScript框架，[D3][d3]无疑成了我的首选。由于D3并没有对于表格的直接渲染，我巧妙地将D3中柱形图的操作应用到表格的设计中，既体现了D3设计美观可交互视图的优越性，又满足了直观表达数据库表格的要求。

为了使动态的数据更加高效地展现在Web界面上，[AngularJS][angular]的data binding思路能够很好地解决这一问题。作为MVC开发思想的领先者，AngularJS将DOM元素与对应的JS代码绑定起来，从而在某JS代码运行之后不必刷新完整的页面，只需要更新下与之对应的DOM元素即可。在提高渲染效率的同时也使得视觉效果更加出色。

通过以上各项技术的结合，一款较高效又美观的基于数据的Web应用就开发出来了。由于所使用技术皆拥有易上手、开源、官方文档易读、社区庞大、实例丰富、代码量小等特点，在短时间内开发出功能强大的应用便成为可能。

[pymysql]: http://dev.mysql.com/doc/connector-python/en/
[flask]: http://flask.pocoo.org/
[d3]: https://d3js.org/
[angular]: http://docs.angularjs.cn/api