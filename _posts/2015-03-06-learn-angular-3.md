---
layout: post
title: '强大的AngularJS（三）—— Controller'
date: 2015-03-06
tags: 'angularjs'
---

AngularJS非常擅长实现模块化，Controller的设计也是基于模块化的，每一个Controller都必须由一个模块老进行创建。

在创建应用的时候，需要对它的作用域设置初始化状态。AngularJS调用控制器的构造函数来设置作用域的初始化状态，这种构造函数是不需要实例化的，它将直接对某个已存在的作用域对象进行调用。

Controller有两个参数，第一个是控制器的名称，这个名称和html结构中的ng-controller的值是对应的。

	<div ng-controller="testCtrl">{{ name }}</div>

	app.controller('testCtrl', function ($scope){
		$scope.name = 'Wiley';
	});

它还有另一种书写形式

	var ctrlCont = ['$scope', function ($scope){
		$scope.name = 'Wiley';
	}];
	app.controller('testCtrl', ctrlCont);

这种方式有一个好处，指明了依赖关系，同时利于避免代码压缩可能出现的问题。这种问题出现的原因是压缩代码时，控制器的参数也会被压缩，这时注入依赖系统就不能正确的识别出服务了。这种问题的另一种解决办法就是，在控制器函数里面给$inject属性赋值一个依赖服务标识符的数组

	ctrlCont.$inject = ['$scope'];

在初学或者实际测试中，我发现Controller并没有什么限制，它可以写具有任何意义的代码，比如数据初始化、业务逻辑、路由、事件操作等等。但官方的意见是，Controller应该纯粹的用来将service、依赖关系、以及其它的对象串联到一起，然后通过$scope把它们关联到view上。

关于Controller的扩展和复用，按照官方的说法，每一个Controller对应一个view，如果有多个Controller使用相同内容时需要考虑将相同的部分抽像成service，然后在Controller中调用这个服务从而实现对Controller的扩展。

官方定义，控制器应用于：

- 设置好作用域对象的初始化状态
- 给作用域对象增加行为

前面已经提到了第一条，第二条给作用域增加行为指的无非是设定作用域的一些方法来实现交互，比如表达式或者ng事件

	<div ng-click="Click()"></div>

上述例子中的Click方法，按官方的意思是应该放在Controller中，因为它属于行为。但这样的DOM交互行为，更应该放在directive（指令）中，具体 [下篇文章](http://dev-note.cn/2015/03/learn-angular-4/) 中会讲到。

所以，本来以为满满的控制器被分离得几乎什么都不剩了，而这恰恰就是模块化思维，将一个臃肿的东西进行有效的分离，便于维护管理。
