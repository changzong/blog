---
layout: post
title:  "JavaScriptES6新特性之循环"
date:   2016-02-29 08:58:00
categories: jekyll update
---

ES6标准即将问世，JavaScript将实现更多方便开发并且强大的改动，其中，最基本的循环语法迎来了更方便的for-of成员。

最早在JavaScript中，我们习惯于使用例如：

```
for (var i = 0; i < array.length; i++){
	//some statement
}
```

来遍历一个数组。后来在ES5中，我们可以使用forEach的内建方法，比如：

```
array.forEach(function(value){
	//some statement
});
```

但是这种方法无法使用例如break来中断循环。

也许你在想，for-in也是JS内建的一个循环方法呀？是的，但是for-in是对数组或对象成员属性的遍历而非对数据的遍历，之后我会用例子进行解释。

在最新的ES6标准中，for-of循环的问世将极大方便我们的遍历操作。其基本语法为：

```
for (var i of array) {
	//some statement
}
```

它是对数据本身进行遍历，并且支持数组、字符串、DOM NodeList对象，以及ES6中最新的Map、Set等。你可以看出，ES6更新的很多新方法和数据结构大都在其他流行的编程语言中广泛应用了。包括for-of循环本身，也很类似于Python中的for-in循环。

如果你想抢先一步体验ES6新特性，你可以在Chrome浏览器中打开JavaScript实验版。

你可以在浏览器地址栏输入chrome://flags，然后找到Enable Experimental JavaScript，点击Enable即可。

接下来在一个新页面中，打开开发者工具，在Console中进行新特性的尝试，比如下图我的实验：

![ES6FOROF](http://7xoylk.com1.z0.glb.clouddn.com/Screen%20Shot%202016-02-03%20at%2009.13.11.png)

可见，for-in循环是对每个元素属性的操作，在数组中返回的就是每个元素的坐标，在对象中返回的是每个元素的key。for-of直接返回每个元素的值。