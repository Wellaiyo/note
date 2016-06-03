---
layout: post
title: '强大的AngularJS（四）—— Directive'
date: 2015-03-07
tags: 'angularjs'
---

上篇文章中有提到为什么不要将DOM操作放在Controller中，比如某个操作（add），它不仅在一个控制器中被需要，在其他的控制器中也同样被需要，那我们就必须将相同的代码拷贝到其它的控制器中。也许你已经想到了，就像Ruby语言的编码原则（Dont Repeat Yourself），我相信这句话适用于所有的编程环境下。然而如果将add操作设置为一个指令独立出来呢？那么只需要在需要的地方调用就OK了。通过构建指令的方式来进行DOM交互和修改，随着业务需求的不断介入，我们就可以立即腾出手来处理复杂性不断增加的应用了。还是原来那句话——进行有效的分离。

Angular利用Directive（指令）来拓展html。

定义指令的四种方式：AEMC，分别表示（属性，元素，样式，注释）

	<hello></hello>
	
	<div hello></div>
	
	<div class="hello"></div>
	
	<!-- directive:hello -->
	<div></div>

AngularJS默认的使用方式是A，在创建带有自己模板的指令的时候，推荐使用E模式。为已有的html标签增加功能的时候，推荐使用A模式。

简单的例子

	testDirective.directive('hello', function (){
		return {
			restrict: 'AEMC',
			template: '<div>Hi, All</div>',
			replace: true
		}
	});

上述例子有一个问题，如果template里面的代码较多时这段代码会显得非常臃肿，AngularJS提供了一种方式来解决这个问题——templateUrl

	testDirective.directive('hello', function (){
		return {
			restrict: 'AE',
			templateUrl: 'hello.html',
			replace: true
		}
	});

如果该指令在很多地方都要调用，可以使用$templateCache将指令缓存起来

	testDirective.run(function ($templateCache){
		$templateCache.put('hello.html','<div>Hi, All</div>');
	});		// .run 注射器加载完所有模块的时候，该方法会被执行一次
	
	testDirective.directive('hello', function (){
		return {
			restrict: 'AE',
			template: templateCache.get('hello.html'),
			replace: true
		}
	});

在这里replace是指替换，那么如果不替换原有代码，而是将原有的代码插入模板中呢？利用tranclude

	<hello>我是原有的内容！</hello>

	testDirective.directive('hello', function (){
		return {
			restrict: 'AE',
			transclude: true,
			template: '<div>Hi, All<div ng-transclude></div></div>'
		}
	});

这样，hello标签中原有的内容会放入ng-transclude的位置。

指令的编译和执行过程：

1. 加入过程
2. 编译过程
3. 链接过程

客户端收到服务器返回的页面请求后，会加载angular.js，然后找ng-app指令，确定angular执行的scope。

然后遍历DOM，寻找所有指令并加入angular自带的缓存中，再根据指令中的template、replace、transclude转换DOM结构。如果存在compile函数则调用。

在AngularJS思想中是非常不推荐在其它地方操作DOM的，应该中指令中操作DOM，指令中有一个link函数，就是用来给开发者操作DOM的。link函数不仅可以操作DOM，还可以绑定事件监听、绑定作用域。

	testDirective.directive('hello', function (){
		return {
			restrict: 'AE',
			template: '<div>Hi, All</div>',
			link: function (scope, element, attrs){
				element.bind('mouseenter', function (){
					console.log('yayayayaya~');
				});
			}
		}
	});

如果该指令在不同的控制器中都被调用了，那么可以这样

	// html
	<div ng-controller="testCtrl">
		<hello toPrint="Print1()"></hello>
	</div>
	<div ng-controller="otherCtrl">
		<hello toPrint="Print2()"></hello>
	</div>

	// controller
	testCtrl.controller('testCtrl', function ($scope){
		$scope.Print1 = function (){
			console.log('我是testCtrl');
		}
	});
	testCtrl.controller('otherCtrl', function ($scope){
		$scope.Print2 = function (){
			console.log('我是otherCtrl');
		}
	});

	// directive
	testDirective.directive('hello',['testCtrl', function (){
		return {
			restrict: 'AE',
			template: '<div>Hi, All</div>',
			link: function (scope, element, attrs){
				element.bind('mouseenter', function (){
					scope.$apply(attrs.toprint);
				});
			}
		}
	}]);

这里有一个小小的坑，<span style="color:#f60;">html中自定义属性如果用的是“驼峰形式”，那么在指令中获取的时候应该都是小写。</span>

在操作完数据，比如增、删、改等之后，要让视图数据更新只要增加下面的代码就OK了

	scope.$apply();

### 指令与指令之间的调用

为了更好让指令的使用更灵活，增加指令之间的交互是必不可少的

	// html
	<add></add>
	<add addTitle></add>
	<add addAuthor></add>

	// angular
	ctrl.directive('add', ['Book', function (Book){
		return {
			scope: {},
			restrict: 'AE',
			template: 'addBook',
			controller: function ($scope){
				this.addBook = function (){
					Book.addBook({title: 'addTitle', author: 'addAuthor'});
				};
				this.addTitle = function (){
					Book.addBook({title: 'addTitle', author: '*******'});
				};
				this.addAuthor = function (){
					Book.addBook({title: '*******', author: 'addAuthor'});
				};
			},
			link: function (scope, element, attr, addCtrl){
				element.bind('click', function (){
					var hasProp = false;  
				    for (var prop in attr.$attr){  
				        hasProp = true;  
				        break;  
				    }
					if(!hasProp){
						addCtrl.addBook({title: 'addTitle', author: 'addAuthor'});
					}
					
					scope.$apply();
				});
			}
		}
	}]);

	ctrl.directive('addtitle', ['Book', function (Book){
		return {
			require: '^add',
			link: function (scope, element, attr, addCtrl){
				element.bind('click', function (){
					addCtrl.addTitle();
				});
			}
		}
	}]);

	ctrl.directive('addauthor', ['Book', function (Book){
		return {
			require: '^add',
			link: function (scope, element, attr, addCtrl){
				element.bind('click', function (){
					addCtrl.addAuthor();
				});
			}
		}
	}]);

可以看到指令中新加了一个scope并设置为空的json对象，这样可以实现，指令之间拥有独立的作用域，相互不发生影响。

这里还用上了一个简单的判断指令是否存在属性，没有就设为默认状态。

require表示该指令要依赖于某指令，不过执行的时候该指令下的link和被依赖指令的link函数都会被执行。然后获取主指令的controller中的方法用link中的另一个参数，这里写的是addCtrl。要注意的是，指令中的controller和angular那个控制器不是一个东西，这里的controller一般在需要将该指令中方法对外调用的时候使用。

关于scope，angular设定了scope绑定策略

<img src="/images/articles/learn-angular-4-1.png" alt="" />

### AngularJS内置指令

- ng-app
- ng-model
- ng-init
- ng-controller
- ng-form
- ng-change
- ng-show/ng-hide
- ng-bind
- ng-if
- ng-switch
- ng-repeat
- ng-href
- ng-src
- ng-class
- ......

[Directive components in ng](http://docs.angularjs.cn/api/ng/directive)