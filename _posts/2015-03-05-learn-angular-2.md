---
layout: post
title: '强大的AngularJS（二）—— Event事件'
date: 2015-03-05
tags: 'angularjs'
---

$on、$emit、$broadcast，要了解它们的用法，先要明白controller中 scope 的定义，

	<div ng-controller="ParentCtrl">	<!-- 父级 -->
	   	<div ng-controller="SelfCtrl">	<!-- 自己 -->
	     	<a class="btn" ng-click="click()">click me</a>	
	     	<div ng-controller="ChildCtrl"></div>	<!-- 子级 -->
	   	</div>
	   	<div ng-controller="BroCtrl"></div>	<!-- 平级 -->
	</div>

这四个controller就表现了四种层级关系，事件由SelfCtrl发出去。

	$scope.$broadcast

发送事件的方法，只能向child controller发送

	$scope.$emit

同样是发送事件的方法，只能向parent controller发送

事件的接收只有一个方法

	$scope.$on

基于上述DOM结构，举个例子

	angular.module('TestApp', [])
		.controller('ParentCtrl', function ($scope){
			$scope.$on('to-child', function (e, d){
				cosole.log('跟我没关系');
			});
		})
		.controller('SelfCtrl', function ($scope){
			$scope.click = function (){
				$scope.$broadcast('to-child', 'haha');
				$scope.$emit('to-parent', 'hehe');
			};
		})
		.controller('ChildCtrl', function ($scope){
			$scope.$on('to-child', function (e, d){
				console.log('I\'m the child, I got it', d);
			});
		})
		.controller('BroCtrl', function ($scope){
			$scope.$on('to-child', function (e, d){
				cosole.log('跟我没关系');
			});
		});

$emit和$broadcast发送的是event和data，同样$on接收的也是这些，所以后面接受的d就是指data数据

在$on的方法中的event事件参数，其对象的属性和方法如下

<img src="/images/articles/learn-angular-2-1.png" alt="" />
