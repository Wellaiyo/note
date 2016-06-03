---
layout: post
title: '强大的AngularJS（七）—— 实用技巧'
date: 2015-03-11
tags: 'angularjs'
---

### 组织项目结构

组织项目结构从来都是因人而异，因项目而异的。但一般也有两种规划方式：第一种是根据代码功能来划分，比如所有的controller都放在controller文件夹中，所有的service都放在service文件夹中，从代码的角度来说这样的划分方式更为整齐；第二种是按照项目功能来划分，比如加入购物车模块的所有controller、service、template等等都放在购物车这个文件夹下。

### controller以及service

通常就是一个页面一个controller是比较好的，假如一个页面有公共的部分，公共部分永远使用一个controller。对于service要划分成两个部分，一个是于服务器交互数据的service，另一个是一些功能性common的内容，其中放置一些自己写的可复用的服务，类似于notify等等。

### 用ng-if代替ng-show/ng-hide

为什么呢？ng-if和ng-show/ng-hide的区别在于，前者会移除DOM节点，而后者只是隐藏、显示DOM节点。这里就牵扯到性能问题了，如果你的项目数据量非常大，那么应该把暂时不必要显示出来的数据，用ng-if false掉，这样可以大大减小页面的压力。简单来说，ng-if比ng-show，加载页面快好几倍。

### ng-class判断赋值

这是一个很好用的东西，也会是项目中用得非常多的东西。ng-class值为类似键值对的，键为要添加的class名，值为布尔值，true则赋值class，false则移除class

	ng-class="{'current': true}"

### ng-repeat中使用filter

ng-repeat中使用filter是很常见的方式，通过一个接口拿到大量数据，然后通过字段去筛选拿到想要的数据结果。

	ng-repeat="food in foods = (foods | filter: { 'name': searchobj.foodfilter } | orderBy: food.sort_order)"

上面的例子是写在html中的，还有一种方式是现在js代码中筛选到想要的数据，然后再ng-repeat到页面html中。相对于第一种，这种方式的性能要好些，代码也更加优雅些。

### 快速定位到某个位置

一般来讲页面内通过 <a id="txt"></a> 这样的形式就可以结合js代码,实现快速定位.在angular中也是通过类似的原理实现

```javascript
var _next = $location.hash();
$location.hash('next-txt');
$anchorScroll();
$location.hash(_next);
```

这样写是因为直接location.hash会导致url变化,页面跳转,所以加了防止跳转的代码

