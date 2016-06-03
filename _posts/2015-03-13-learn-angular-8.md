---
layout: post
title: '强大的AngularJS（八）—— $apply'
date: 2015-03-13
tags: 'angularjs'
---

在angularjs开发过程中，经常会遇到这样的问题，给$scope绑定了数据，变更数据的时候视图上没有刷新，于是主动调用 $apply 去更新视图。但有时候调用 $apply 会报错，不调用的时候视图也会更新。

很郁闷，到底什么时候应该调用$apply什么时候不需要调用呢？

angularjs使用了 $apply 来监听数据的变化情况，如果监听到数据变化，那么就直接更新到视图。但会存在那么一些情况，就是非angularjs直接变更的数据，比如：外部的回调、jQuery的UI事件、DOM事件，这些情况angularjs是不会主动去刷新视图的。所以需要主动的使用 $apply 去更新视图变化。

关于$apply的使用提示：

- 不要频繁调用，angularjs在其生命周期内正常运行时，调用 $apply 会报错

- 在angularjs外部的代码中更新了数据时，必须要使用 $apply ，命令angularjs去刷新视图

- 尽量把要执行的代码传递给 $apply 去执行，而不要自已执行那些函数然后再调用 $apply 

```javascript
$scope.$apply(function() {
	$scope.test = 'test';
	forTest();
});
```

这种方式与直接在后面使用$scope.$apply()效果是一样的，但有一个重大的不同。那就是这种方式在调用后续函数时，能捕捉错误，而直接在后面调用的方式会忽略所有的错误。
