---
layout: post
title: "JavaScript中的闭包"
date: 2015-05-22
tags: "javascript"
---

闭包一直都是javascript开发者的关注点，也是一大难点。对于初学者来说，相对于javascript的其他部分，闭包明显稍稍难理解一点。

闭包和javascript中的变量作用域是相关联的，所以要先明白这其中的关系。

我们都知道，一般变量分为局部变量和全局变量，全局变量作用域指向的是window对象，局部变量作用域指向的是函数对象，而在javascript中，父级对象无法访问子级对象中的变量。

	function fn1(){
		var num = 1;
		function fn2(){
			console.log(num); 
		}
		return fn2;
	}
	var result=fn1();
	result();	// 1

网上存在各种对闭包的定义和诠释，但我感觉都很抽象。很难说清闭包是什么，但什么时候会形成闭包，这点倒容易理解多了。

一个函数内嵌套了函数，而这个嵌套函数又被在它的上层函数外的地方引用，此时就形成闭包。

嵌套函数可以访问上层函数定义的局部变量、参数等数据，而按道理上层函数在调用结束后将会被回收（销毁），但因为存在引用关系，所以嵌套函数和包含它的函数不会被回收（javascript的回收机制：被缓存起来的变量或对象不会被回收）。这时定义在上层函数的局部变量还是有效的。如果被外部引用的嵌套函数定义的一些方法对它的上层函数的变量进行了访问，那么这个行为是有效的。也就是说一个函数读取了其他函数内部的变量。

这便是闭包的一大用途——缓存变量。

### 闭包中this指向window

	var str = 'Global';
	var obj = {
	    str: 'Object',
	    getStr: function() {
			return (function() {
				return this.str;
			})();
	    }
	};
	console.log(obj.getStr());	// 'Global'

上述例子形成了闭包，thisArguments等于undefined。

《ECMAScript® 2015 Language Specification》中明确指出了，如果thisArguments是undefined，则thisValue就是[[globalThis]]。而globalThis就是Global Object，Global Object就是window。
