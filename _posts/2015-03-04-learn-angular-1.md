---
layout: post
title: '强大的AngularJS（一）—— 路由配置'
date: 2015-03-04
tags: 'angularjs'
---

AngularJS通过操作锚点来进行路由，从而实现不刷新页面的路由、视图切换效果。

	var app = angular.module('ngRouteExample', ['ngRoute'])
	    .config(function($routeProvider, $locationProvider){
	      	$routeProvider
	          	.when('/index', {
	              	templateUrl: 'index.html',
	              	controller: 'indexCtrl'
	          	})
	          	.when('/user', {
	              	templateUrl: 'user.html',
	              	controller: 'UserCtrl'
	          	})
	          	.otherwise({
					redirectTo: '/index'
	          	});
	    });

上述代码是angular自带的ngRouter的实例代码

路由地址类似

	/#/index

html中要存有带 ng-view 的节点，用于存放加载进来的视图。

UI-Router是 [Angular-UI](https://github.com/angular-ui) 提供的客户端路由框架，它弥补了ngRouter的很多不足

- 视图不能嵌套，导致可能引发 $scope 会发生不必要的重新载入
- 同一url下不支持多个视图，比如我们希望导航栏部分用一个视图，内容部分用一个视图

完整例子

	var app = angular.module('uiRouterExample', ['ui.router'])
		.config(function ($stateProvider, $urlRouterProvider){
			$urlRouterProvider.otherwise('/login');
			$stateProvider
				.state('index', {
					url: '/index',
					views: {
						'': { templateUrl: 'login.html' },
						'topbar': { templateUrl: 'template/topbar.html' },
						'main': { templateUrl: 'template/main.html' }
					}
				})
				.state('login', {
					url: '/login',
					templateUrl: 'login.html'
				})
				.state('about', {
					url: '/about',
					templateUrl: 'about.html'
				})
				.state('admin', {
					url: '/admin',
					templateUrl: 'admin.html'
				});
	});

在非服务环境下，上述代码会莫名报错，所以最好开启 [http-server](https://github.com/indexzero/http-server) 环境开发 AngularJS 程序。

之前一直以为AngularJS是用来做SPA应用的，所以路由中带 #/ 才是正常的。但是后来发现很多产品都是AngularJS开发的，同时它们的路由却和正常的web应用路由一样，需要刷新的。想了很久都没明白怎么处理的，直到看了 [jsGen](https://github.com/zensh/jsgen) 这个开源项目的路由实现后，发现路由中的一句代码

	$locationProvider.html5Mode(true);

