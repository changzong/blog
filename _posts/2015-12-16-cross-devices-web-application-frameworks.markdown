---
layout: post
title:  "跨设备Web应用开发的框架选择"
date:   2015-12-16 14:21:00
categories: jekyll update
---
## 前端篇
***
开发Web应用时，在眼花缭乱的前端框架选择上花费大量的时间是件很正常的事。但是有一点要注意的是，在选择某一框架前一定要问一问自己为什么需要用到这个框架，它能给我的开发带来怎样的优势，它又跟同类型的其他框架相比较有什么样的优缺等等。

为了使这一过程简单一些，我将主流的一些框架按照我开发过程中的需求分类进行对比和选择。

### UI：
作为最前端的UI设计和插件框架，考虑到跨设备的需求，Bootstrap无疑是目前较流行和强大的一款了它良好的自适应能力能给开发过程减轻不少难度，并且使用者的反馈也认为它的移动端性能相比较JQueryUI也是较好的。由于我的应用中会包含大量的过滤器和按钮，Bootstrap丰富的小插件和主题也能给我提供不少选择，其可定制性也会优于JQueryUI。但是由于上手可能没有JQueryUI快速，需要较长的时间来学习。但我认为作为UI框架，再复杂也不会耗费太多的时间。

### DOM操作：
在DOM的选择和操作上，JQuery成了不二之选。它丰富的DOM处理函数极大方便了开发过程。并且它与其他一些框架能够良好的兼容起来，甚至很多主流框架都是以JQuery为基础来使用的。

### 逻辑开发：
我是AngularJS的忠实追随者，它拥有纯粹的MVC开发思想，非常适合应用在展现数据类型的开发过程中。它特有的双向数据绑定（two ways data binding）特征能够实时、局部、高效地响应数据的变化来更新界面。最近很火的另一个框架ReactJS我也接触了一些，它并不是AngularJS的替代品，或者说我认为它们能够很好地并存使用。React提供了新的开发思路，将界面分区域，每个局部又拥有虚拟DOM和真实DOM，据称能够更加高效响应数据更新界面。但在本次开发过程中，在没有发现Angular不能满足现有条件之前，暂不考虑React。

### 表格：
由于本次开发的应用中含有大量表格展现数据的部分，选择能够很好地过滤和排序表格数据的框架是必要的。之前接触的EasyUI和datagrid filter可以完成基本功能的实现，但是它界面和结构单一，也不是响应式的，所以需要寻找可替代品。目前暂时选定了基本的HTML5 table元素并在其中添加AngularJS和Bootstrap属性用来实现过滤排序和样式的美化。[samu-angular-table][samu]等一系列基于Angular的表格实现能够给予一定的支持。

### 图表：
除了表格，另一大部分功能来自于图表的实现。本人之前有过较丰富的D3开发经验，为其灵活多变的可视化功能所吸引。但是就目前的需求来看，图表的样式可能只限于基本的一些类别，包括柱状图、线形图、饼图、面积图以及它们之间的混合图等，所以我们暂时选用Highcharts这一框架。它包含了比较丰富的图表库，并且是以JSON为输入数据格式的，符合我们的需要。在开发过程中若有特殊图表的需求再选用D3或者WebGL技术。


## 后端篇
***
由于本人之前只有Python后端编程经验，接触过的Web应用开发后端也只有Django和Flask两种。在本项目中，为了更好地与共同开发人员对接，我决定学习Java Web开发，并选择Spring作为主要开发框架用来处理URL路径和请求响应事件。

后端开发思路上我认为RESTful API的思想是很值得推荐的。REST认为我们的客户端和服务端之间的通信过程由资源和对资源的操作行为来构成。资源泛指我们能够获得的一切名词，它可以有多种存在的形式。比如消息资源可以用HTML、XML、JSON、txt等形式展现出来。而对资源的操作则是我们常用的HTML请求行为，如POST、GET、PUT、DELETE这些动词。通过资源的名称，资源的URL地址以及请求行为，我们就能实现基本的RESTful API开发思想。通过下面这样的地址（来自[Client-Server Web Apps with JavaScript and Java][book1]），前端能够获得从服务器端返回的数据（如JSON）进行进一步处理和界面的渲染。

``<scheme>://<user>:<password>@<host>:<port>/<path>;<parameters>?<querykey/value pairs>#<fragment identifier>``

## 总结
***
通过上述前端框架和Java服务器端以及RESTful API作为中间层，希望能够开发出效果出色的跨平台Web应用。

不管选用怎么样的框架，在能够符合项目的开发要求前提下尽可能轻量化高效率是关键。当然，自己喜欢用起来顺手的技术也是快速开发出优秀产品的关键。但是不论怎样，不停地学习总结新的知识永远是进步的前提。作为一名Full Stack Developer，更是如此。


[samu]: http://samu.github.io/angular-table/examples/examples.html
[book1]: http://www.amazon.com/Client-Server-Web-Apps-JavaScript-Java/dp/1449369332