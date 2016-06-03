---
layout: post
title: "javascript代码杂谈（一）"
date: 2015-10-18
tags: "javascript"
---

javascript中函数调用有4种模式：方法调用、正常函数调用、构造器函数调用、apply/call调用。

方法调用是指调用某个对象的方法

	var obj = {
		a: 'a',
		b: 'b',
		fn: function (){

		}
	};

	obj.fn();

正常的函数调用估计是大家最熟悉的方式

	function fn(){
		console.log('This is a Function');
	}

	fn();

构造函数调用相对来说多了一个new，其它也没什么差别

	function Fn(agu){
		this.agu = agu;
	}

	var _fn = new Fn(5);

值得注意的是，这里在构造函数中使用了this，如果不是new调用的话，就是一个比较坑的javascript中this指向window的例子。但是这边用了new，所以this会指向这个新创建的对象_fn。

在javascript中，所有的事务（比如：字符串、数值、数组、函数....）都是对象。每个函数都有一个公共的prototype，这个原型链自带了一些属性，像bind、call、apply。

apply是一个很强大的属性，它可以给函数传递一个参数数组，并可以通过apply的第一个参数来设置函数中this的指向。

	function fn(agu){
		this.agu = agu;
	}

	var obj = {'agu': 1};
	fn.apply(obj, [5]);
	console.log(obj.agu);

打印出来的值是5，我们发现函数fn中的this从window变成了obj。

call和apply很类似，他们都可以改变this的绑定，唯一的区别在于，call的第二个参数不是一个数组，而是不定参数的个数。可以发现，在应用上apply比call更方便。

	function fn(agu){
		this.agu = agu;
	}

	var obj = {'agu': 1};
	fn.call(obj, 5);
	console.log(obj.agu);

bind和call属性其实也很类似，它们的参数调用方法一致。不过bind会生成一个新的函数，而不是直接改变原来的函数。

	function fn(agu){
		this.agu = agu;
	}

	var obj = {'agu': 1};
	var _fn = fn.bind(obj, 5);
	_fn();
	console.log(obj.agu);

前面提到的prototype是javascript重点中的重点，因为很多的编程方法都是基于它来实现的。现在来看下，JavaScript prototype背后的工作原理

javascript中的继承是基于原型链来实现的，也就是prototype，简单来说，prototype是javascript中的函数Function的一个保留属性，而且其值是一个对象。

	function fn(){
		// code
	}
	console.log(typeof fn.prototype);

于是我们可以得出一个结论，prototype是一个对象。

而且prototype是可以被任意修改的。

	var Obj = function (){};

	Obj.prototype = {
		init : function (){
			console.log('对象已被初始化');
		}
	}

	var o = new Obj();
	o.init();

那么来看下，prototype背后的工作原理是怎样的

一般我们都会基于某个构造函数new一个新的对象，javascript引擎首先遇到了关键字new后，马上开辟出一块内存，创建一个空对象(并且将this指向这个对象)，接着执行构造函数，对这个空对象进行构造(构造函数里面有什么属性和方法都一一给这个空白对象装配上去，这也就是为什么构造函数叫“构造函数”的原因)。值得注意的是，按照标准来说，构造函数应该以大写字母开头，虽然这个问题不会造成报错。

在创建对象和构造对象之间还有一步不为人知的操作，就是获取prototype。这里有必要提一下对象的内置属性__proto__，__proto__除IE外都是可以访问的。对象创建的时候，对象的__proto__属性指向了构造函数的prototype对象，于是对象拥有了prototype的属性和方法，给对象添加prototype是无效的，因为并没有继承prototype这个功能。

所以，new创造一个对象的过程应该是这样的

	var o = new Obj();

1. 引擎解析遇到new，开辟内存创建空对象，将this指向该对象
2. 将__proto__指向prototype对象，获取其属性和方法
3. 执行构造函数内部的代码，并开始构造空对象
4. 将构造好的对象赋值给等号左边的变量

今天又被点醒了：<span style="color:#f60">知其然，知其所以然</span>
