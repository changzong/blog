---
layout: post
title:  "在EasyUI Datagrid Filter中添加日期范围选择过滤器"
date:   2015-12-15 08:54:00
categories: jekyll update
---
EasyUI是一个简单常用的前端框架，其丰富的插件极大方便了我们开发出美观易用的轻量级Web应用。在制作可过滤表格时我们选用了它的插件之一，Datagrid Filter，它提供了在我们的表格中添加过滤器的功能，但由于可选插件受限于EasyUI，我们在很多时候希望能够实现更多方便的功能，尤其在过滤日期范围的时候，我们并不希望通过选择一个小于它的日期点，再选择一个大于它的日期点来实现。这是就需要改写Datagrid Filter，在其中添加新的过滤器。

在EasyUI的论坛中找到了一个实现此功能的方法[Create datebox range in datagrid][forum]，它新添加了一个叫做dateRanger的过滤器，在其中定义了两个EasyUI中的calendar插件，分别表示起始日期和结束日期，并用combo插件将它们组合起来显示，最后将选择的结果以冒号形式隔开。在操作operator时添加新的Between操作并拆开含有冒号的字符串为两个日期与表格中的日期进行比较。这样，只要后端将dateRanger这个新的过滤格式传过来，它便启用这个新添加的日期范围选择器。

在使用后发现，虽然功能上没有问题，但是样式上看起来非常奇怪，并不像EasyUI原生的样式。原因是它在声明calendar插件时并没有用到EasyUI中规定的声明格式，即class要用easyui-calendar。我们将下面代码改一下：

{% highlight JavaScript %}
$('<div class="c1" style="width:50%;float:left"></div><div class="c2" style="width:50%;float:right"></div>').appendTo(p);
{% endhighlight %}

改为：

{% highlight JavaScript %}
$('<div class="easyui-calendar" id="c1" style="width:50%;float:left"></div><div class="easyui-calendar" id="c2" style="width:50%;float:right"></div>').appendTo(p);
{% endhighlight %}

再将代码其他部分里选择c1和c2的class名改为id就可以实现原生样式的效果了。

结果如下图：
![Daterangepicker](http://7xoylk.com1.z0.glb.clouddn.com/daterangepicker.JPG)

[forum]: http://www.jeasyui.com/forum/index.php%3Ftopic%3D3995.0