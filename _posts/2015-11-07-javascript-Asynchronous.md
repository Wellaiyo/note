---
layout: post
title: "javascript异步编程"
date: 2015-11-07
tags: "javascript"
---

我们都知道，javascript的执行环境是单线程的。一次只能执行一个任务，下一个任务要等上一个任务执行完毕才能继续执行。这样会导致一个问题就是如果某一个程序执行缓慢或者卡死，那后续的程序就要一直等下去~

于是就有了异步编程，异步编程简单来说就是利用一个或者多个回调函数，让函数主体先执行，等所有的主体函数都执行完毕之后再去执行回调中的内容。这样就不会造成执行一堆程序导致后续的代码停滞的状态了，比如客户端加载某个程序时间过长，导致页面渲染严重滞后甚至是浏览器卡死。服务器端异步是唯一的模式，为什么这么说呢？服务器会接收到各种各样的请求，如果同时执行的话，服务器性能会急剧下降，最后失去响应。

[阮一峰前辈关于异步编程的博文](http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)很好的指明了四种异步编程的方法。

- 回调事件
- 事件监听
- 发布/订阅
- Promise模式

最后也提到了Promise模式比前面三种方法更好，也更难编写和理解。前段时间在Coding的时候也遇到了多个回调的时候应该怎么去处理，找到的方法就是利用Promise模式。

Promise模式的核心是存在一个promise对象，这个对象有一个then方法用于指定回调函数

	fn1().then(fn2);

可以将一个函数的返回值作为参数传递给下一个函数，并将下一个函数的返回值作为参数再传递给下下个函数，这样就像一条链子一样，仅仅扣在一起，又能无限延伸下去。

Promise模式的代码实现

```javascript
//constructor
var Promise = function() {
    this.callbacks = [];
}

Promise.prototype = {
    construct: Promise,
    resolve: function(result) {
        this.complete("resolve", result);
    },

    reject: function(result) {
        this.complete("reject", result);
    },

    complete: function(type, result) {
        while (this.callbacks[0]) {
            this.callbacks.shift()[type](result);
        }
    },

    then: function(successHandler, failedHandler) {
        this.callbacks.push({
            resolve: successHandler,
            reject: failedHandler
        });

        return this;
    }
}
```

下面来测试一下

```javascript
var promise = new Promise();

var delay1 = function() {
    setTimeout(function() {
        promise.resolve('数据1');
    }, 1000);
    return promise;
};

var callback1 = function(re) {

    re = re + '数据2';
    console.log(re);
};

delay1().then(callback1);
```

理解上述代码，请[参考](http://segmentfault.com/a/1190000003028634)

深入理解：

- [Promise/A的误区以及实践](http://qingbob.com/promise-a-misunderstanding-and-practical/)
- [JavaScript异步编程的Promise模式](http://www.infoq.com/cn/news/2011/09/js-promise/)
