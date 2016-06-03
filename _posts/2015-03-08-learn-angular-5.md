---
layout: post
title: '强大的AngularJS（五）—— Service'
date: 2015-03-08
tags: 'angularjs'
---

Service简而言之就是——单例对象。

比如[大漠老师](http://damoqiongqiu.iteye.com/category/287942)的这个例子

```javascript
module.service( 'Book', [ '$rootScope', function( $rootScope ) {
    var service = {
      	books: [
        	{ title: "Magician", author: "Raymond E. Feist" },
        	{ title: "The Hobbit", author: "J.R.R Tolkien" }
      	],

      	addBook: function (book) {
        	service.books.push(book);
        	$rootScope.$broadcast( 'books.update' );
      	}
   	}
   	return service;
}]);
```

这里假设了一个对象——Book，要对项目中的Book对象进行操作，所以我们定义了一个Book对象，其中有我们对该对象的数据的初始化和对对象的一些操作方法。

通过scope来管理数据是一种非常粗暴的方式，集中所有同类的数据在service中明显更为优雅，也更易于维护管理。我们还可以在service中定义一些方法来操作这些数据，比如增加、删除、更新等等。

关于服务，书写方面主要有两种方式。一种是服务方法，也就是上述例子。

还有一种是工厂方法

```javascript
module.factory( 'Book', [ '$rootScope', function ( $rootScope ){
	var service = {
      	books: [
        	{ title: "Magician", author: "Raymond E. Feist" },
        	{ title: "The Hobbit", author: "J.R.R Tolkien" }
      	],

      	addBook: function (book) {
        	service.books.push(book);
        	$rootScope.$broadcast( 'books.update' );
      	}
   	}
   	return service;
}]);
```

实际上还有另外几种方法，比如 $provider 等，这里就不做详细介绍。

如果关于服务的理解还不够彻底，那么再直白一点。

AngularJS中的服务其实就是提供一种方式抽取共用类库，对外提供某些特定功能。

比如传统开发中，我们会定义一些工具类方法，这些方法可能会在很多地方都被需要，平常我们会写一个utility类，再将其存入一个全局变量中，这样需要的地方调用就行了。而AngularJS中的service则是直接创建了一个utility服务，你可以在任何地方通过依赖注入调用这个utility服务中的方法。
