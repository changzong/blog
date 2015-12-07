---
layout: post
title:  "React and AngularJS"
date:   2015-12-07 17:33:16
categories: jekyll update
---
Something about data binding:

MVC and Data binding is the core concept in AngularJS. With mapping UI with specific part of JavaScript code, the view can be partially updated when the corresponding JS code is activated, which enhance the performance of your Web application.

What about React:

However, ReactJS is using the concept of components and virtual DOM. It assumes your UI can be splitted into multiple components and each of them can be embeded into another parent one. When some JavaScript code is executed, the virtual DOM of the corresponding components is changed. When it detects diff between the virtual DOM before and after, it updates that DOM element. So I think React's idea is from data binding. Facebook claims this way has much higher performance. 

Check [ReactJS][react] and [AnguarJS][angular]

[react]: http://facebook.github.io/react/docs/getting-started.html
[angular]: http://docs.angularjs.cn/api