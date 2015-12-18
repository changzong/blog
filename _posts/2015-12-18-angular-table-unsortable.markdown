---
layout: post
title:  "Angular表格中Orderby失效的一个问题"
date:   2015-12-18 15:41:00
categories: jekyll update
---
用过AngularJS做table的人可能遇到这样一个问题，如果我把表格中头部的列一个一个写出来，用`ng-click`分别赋给它们相应的列名，那么点击表格头部的时候是可以做到正常的排序效果的。代码如下：

{% highlight HTML %}
<table>
    <thead>
        <th>
            <a href="#" ng-click="sortType = 'id'; sortReverse = !sortReverse">
                Id
                <span ng-show="sortType == 'id' && sortReverse"></span>
                <span ng-show="sortType == 'id' && !sortReverse"></span>
            </a>
        </th>
        <th>
            <a href="#" ng-click="sortType = 'name'; sortReverse = !sortReverse">
                Names
                <span ng-show="sortType == 'name' && sortReverse"></span>
                <span ng-show="sortType == 'name' && !sortReverse"></span>
            </a>
        </th>
    </thead>
    <tbody>
        <tr ng-repeat="x in data | orderBy:sortType:sortReverse">
            <td align="center"><span>{{ x.id }}</span></td>
            <td align="center"><span>{{ x.name }}</span></td>
        </tr>
    </tbody>
</table>
{% endhighlight %}

在每一个`th`中点击事件都会赋给`sortType`相应的列名，并在`tr`中作为`orderBy`的条件。

但是如果我有很多列怎么办？我们肯定不想把它们一一写出来吧。于是我想写成下面代码：

{% highlight HTML %}
<table>
    <thead>
    <th ng-repeat="i in column" style="text-align: center">
        <a href="#" ng-click="sortType = i; sortReverse = !sortReverse">
            {{i}}
            <span ng-show="sortType == i && sortReverse"></span>
            <span ng-show="sortType == i && !sortReverse"></span>
        </a>
    </th>
    </thead>
    <tbody>
    <tr ng-repeat="x in data | orderBy : sortType : sortReverse">
        <td align="center" ng-repeat="y in column"><span>{{ x[y] }}</span></td>
    </tr>
    </tbody>
</table>
{% endhighlight %}

在`th`中同样添加了`ng-repeat`循环实现表格头部的生成。但是这样做的结果是点击表头无法实现排序的效果了。

通过查询终于明白，`ng-repeat`会创建一个新的scope，而你的`ng-click`赋值是对于每个头部的自己的scope中实现的。所以有了`ng-repeat`之后，头部的结构依然可以创建出来，但是点击事件就不会再起作用了。需要在点击事件发生时运行一个函数进行赋值就可以了。

{% highlight HTML %}
<table id="tableManager">
    <thead>
    <th ng-repeat="i in column" style="text-align: center">
        <a href="#" ng-click="toggleSort(i)">
            {{i}}
            <span ng-show="sortType == i && sortReverse"></span>
            <span ng-show="sortType == i && !sortReverse"></span>
        </a>
    </th>
    </thead>
    <tbody>
    <tr ng-repeat="x in data | orderBy : sortType : sortReverse">
        <td align="center" ng-repeat="y in column"><span>{{ x[y] }}</span></td>
    </tr>
    </tbody>
</table>
{% endhighlight %}

然后在JavaScript中实现toggleSort函数

{% highlight JavaScript %}
$scope.toggleSort = function(col) {
    if($scope.sortType === col){
        $scope.sortReverse = !$scope.sortReverse;
    }
    $scope.sortType = col;
}
{% endhighlight %}

之后运行就可以排序了。

** 特别感谢StackOverFlow！