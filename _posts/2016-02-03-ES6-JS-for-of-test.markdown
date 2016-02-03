---
layout: post
title:  "JavaScriptES6新特性之循环"
date:   2016-02-10 09:20:00
categories: jekyll update
---

在使用AngularJS时会用到两个内置函数$interval和$timeout，然后如果不充分理解它们的工作过程的话会给开发带来一些麻烦。有时候为了做出一些稍微复杂的效果，在设置完时间事件后会在特定条件下取消它们，对于interval来说，只需要将一开始设置时间间隔的函数赋值给一个变量，然后再将它通过cancel函数取消掉就好。但是对于timeout，如果使用局部变量，在不同的scope中，cancel函数是不会起作用的。

解决方法主要有：

1. 尽量使用$interval而不使用$timeout，可以设置interval的次数为1来达到timeout的效果。

2. 将interval和timeout的函数赋值变量定义在全局，以实现变量时时有效的目的。

AngularJS的这两个函数是对JavaScript的setInterval和setTimeOut的封装。

Check [Angular $interval][interval] and [Angular $timeout][timeout].

[interval]: http://docs.angularjs.cn/api/ng/service/$interval
[timeout]: http://docs.angularjs.cn/api/ng/service/$timeout