---
layout: post
title:  "用AngularJS加Bootstrap分页做table"
date:   2016-02-01 16:06:00
categories: jekyll update
---

AngularJS提供的方法可以让table元素轻松具备排序等功能，但是却没有很好的分页插件，所以决定自己手工写一个分页逻辑。所用的样式就是Bootstrap提供的分页元素。

直接上代码：

{% highlight HTML %}
<nav>
  <ul class="pagination">
    <li><a href="#" aria-label="Previous"><span aria-hidden="true">&laquo;</span></a></li>
    <li ng-repeat="p in pageNumber" ng-click="togglePage(p)" id="page{{p}}"><a href="#">{{p}}</a></li>
    <li><a href="#" aria-label="Next"><span aria-hidden="true">&raquo;</span></a></li>
  </ul>
</nav>
{% endhighlight %}

这段就是用Bootstrap的分页样式配合了Angular中定义的变量，其中pageNumber是页数array，如[1,2,3,4,5]，这样循环添加li元素。函数togglePage()主要做两件事：1. 点击其他页数的时候样式的变化，2. 当前页中数据的更新。

{% highlight JavaScript %}
$scope.togglePage = function(pageNum) {
	for(var i in $scope.pageNumber) {
		$("#page"+$scope.pageNumber[i]).attr("class", "");
	}
	$("#page"+pageNum).attr("class", "active");
    	if (pageNum == $scope.pageNumber[$scope.pageNumber.length-1]) 
    		$scope.currentData = $scope.data.slice($scope.itemsByPage*(pageNum-1));
    	else
    		$scope.currentData = $scope.data.slice($scope.itemsByPage*(pageNum-1), $scope.itemsByPage*pageNum);
}
{% endhighlight %}

当点击事件发生时，被点击的页号颜色加深，即使用Bootstrap样式active，其他页号变浅。同时设定当前页数据currentData，它是从对整体数据data上分割下来的。

到这还很简单，我们当然还需要排序功能：

{% highlight HTML %}
<thead>
    <tr>
      <th ng-repeat="i in column" ng-click="toggleSort(i)" id="table_head_{{i}}">{{i}}</th>
    </tr>
</thead>
<tbody>
    <tr ng-repeat="j in currentData | orderBy:sortType:sortReverse">
        <td ng-repeat="k in column" ng-click="popGraph(j[k], k)">
            {{j[k].toString().slice(0,25)}}
        </td>
    </tr>
</tbody>
{% endhighlight %}

{% highlight JavaScript %}
$scope.toggleSort = function(col) {
	$scope.sortType = col;
	$scope.sortReverse = !$scope.sortReverse;
};
{% endhighlight %}

当点击表格的头部时，通过toggleSort()函数设置当前列的名字变量和排序方式，发送个table体，进行排序。可以看到这里表格体用的数据是当前页数据currentData，目的是当进行分页操作的时候，currentData会改变，从而引发数据绑定来更新局部页面，这是AngularJS的特性。

但是这里有个问题，排序操作只是针对当前页的内容来进行的而非全部数据。这会导致排序只排一页的数据，这显然不合常理。

为此我们需要在排序操作发生时也对currentData进行更新，在排序过的数据上分割。于是在toggleSort()加入两行代码：

{% highlight JavaScript %}
$scope.toggleSort = function(col) {
	$scope.sortType = col;
	$scope.sortReverse = !$scope.sortReverse;
	// Everytime sorted, update $scope.data, then go back to page1, meanwhile update $scope.currentData
	$scope.data = $filter('orderBy')($scope.data, $scope.sortType, $scope.sortReverse); 
	$scope.togglePage(1);
};
{% endhighlight %}

这样使用Angular的$filter服务对全局数据data进行排序更新，然后调用分页函数togglePage()强制指向第一页，同时可以从排序过的数据上分割数据作为新的currentData，进一步触发数据绑定，更新表格。

通过这次尝试我对Angular的机制有了更进一步的认识。