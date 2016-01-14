---
layout: post
title:  "Echarts事件响应"
date:   2015-01-14 08:48:00
categories: jekyll update
---

Echarts是百度开发的一款数据可视化库，个人感觉还不错，能够自定义的部分很全面，虽然不像D3那样完全可定制，但是基本上能想到的要求都能通过option里的配置或者通过API实现。

最近在做一款基于Echarts地图的数据迁移可视化Web应用，需要根据要求随时改变要显示的城市迁移图。通过对option里legend的selected配置，可以实现选择某一些特定的城市通过某种方式比如循环播放的形式展现数据。但是，如果用户点击了legend会发生什么呢？这种用户主动操作的方式无疑会使自动展现形式变得变得不那么炫酷。所以我们不得不对用户的行为进行监听并作出响应。好在Echarts提供了绑定事件处理函数的API。

通过以下格式响应用户鼠标点击某legend：

{% highlight JavaScript %}
require(
	[
	    'echarts',
	    'echarts/chart/map'
	],
	function(ec) {
		var ecConfig = require('echarts/config');
	    var myChart = ec.init(document.getElementById('main')); 
		var option = setChartOption($scope);
		myChart.setOption(option); 
		myChart.on(ecConfig.EVENT.LEGEND_SELECTED, function(param){
			param.target // 城市名称
			param.selected // legend.selected object
		});
{% endhighlight %}

在最新的Echarts 3.0版本中，事件名称统一用小写字符串代替，如：‘click’，‘legendselected’