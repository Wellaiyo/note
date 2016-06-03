---
layout: post
title: '$http.post和$.ajax的迥异'
date: 2016-01-19
tags: 'angularjs'
---

在做angularjs开发的时候，遇到一个比较难搞的问题：ajax请求数据的时候，用jQuery的$.ajax正常访问，但是用angularjs自带的$http.post就拿不到数据了，报错。

想了很久原因，应该是出在我对于angularjs API的不熟悉，Google搜索了一下，找到答案！

angular和jquery的ajax请求是不同的。

在jquery中，官方文档解释contentType默认是 application/x-www-form-urlencoded; charset=UTF-8

而参数data，jquery是进行了转换的。

也就是说，我们将数据传递给后端的时候，jQuery已经为我们传递的数据做好了处理，将javascript对象转换成了字符串。

而在angular中，$http的contentType默认值是 application/json;charset=UTF-8

要想angularjs达到像jQuery一样的效果，要做如下处理：

1. 修改Content-Type为application/x-www-form-urlencoded; charset=UTF-8

2. 请求参数的格式 key=value的格式，如果，多个，则使用&连接

没错，将其序列化就OK了。

那么解决方案可以是这样的：

1. 使用jQuery的ajax，如果项目没必要用到一个jQuery库，也可以定制一个jQuery ajax

2. 在后端将传递过来的数据序列化，不过这样的做法明显不合适

3. 修改angularjs的$httpProvider

第三种应该算是比较合理的办法，毕竟谁挖的坑谁来填。

	var app = angular.module('MyApp', [], function ($httpProvider){
		$httpProvider.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=utf-8';
		/**
	  	* The workhorse; converts an object to x-www-form-urlencoded serialization.
	 	* @param {Object} obj
	  	* @return {String}
		*/ 
		var param = function(obj) {
			var query = '', name, value, fullSubName, subName, subValue, innerObj, i;
	      
	    	for(name in obj) {
	    		value = obj[name];
	        
	      		if(value instanceof Array) {
	        		for(i=0; i<value.length; ++i) {
	          			subValue = value[i];
	          			fullSubName = name + '[' + i + ']';
	          			innerObj = {};
	          			innerObj[fullSubName] = subValue;
	          			query += param(innerObj) + '&';
	        		}
	      		}else if(value instanceof Object) {
	        		for(subName in value) {
	          			subValue = value[subName];
	          			fullSubName = name + '[' + subName + ']';
	          			innerObj = {};
	          			innerObj[fullSubName] = subValue;
	          			query += param(innerObj) + '&';
	        		}
	    		}else if(value !== undefined && value !== null)
	        		query += encodeURIComponent(name) + '=' + encodeURIComponent(value) + '&';
	    		}
			return query.length ? query.substr(0, query.length - 1) : query;
		};

		$httpProvider.defaults.transformRequest = [function(data) {
			return angular.isObject(data) && String(data) !== '[object File]' ? param(data) : data;
		}];
	});

现在OK了，可以正常使用了，也不要去麻烦后端的各位大神了！
