---
layout: post
title: "前端模块化历程"
date: 2015-12-03
---

> 需求是推动进步的主要动力。这句话适合每一个行业，甚至是这个社会的发展。

前端的模块化是怎样炼成的呢？

在web初期时代，javascript主要就是为了实现简单的页面交互逻辑。随着设备性能以及用户体验需求的高速提升，前端代码日益庞大，复杂的交互逻辑让原本的javascript解决方案变得有点捉襟见肘。为了更好的组织前端代码，模块化出现了，它的出现是必然的。

javascript设计者初期并没有为了其提供模块这样的一种功能，甚至他都不看好javascript这门脚本语言。据说，设计javascript只花费了十天时间。OK，这都是题外话了。

### 函数封装

我们希望javascript实现的每一个功能都可以相互独立出来，这样我们调用的时候，可以只调用单个函数方法。

```javascript
function fn1(){
	// code
}

function fn2(){
	// code
}
```

但这样的方式首先就引发出了全局变量污染的问题，而且模块之间没什么关系或者没什么明显关系。为了解决全局变量的问题，可以将所有的方法都存于一个对象里面。

```javascript
var theModule = {
	a: 'a',
	b: 'b',
	fn1: function (){
		// code
	},
	fn2: function (){
		//code
	}
};
```

看似不错，如果theModule.a = 'aa'，不是就改变了模块内部吗？于是

```javascript
var theModule = (function (){
	var a = 'a';
	var b = 'b';
	function fn1(){
		// code
	}
	function fn2(){
		//code
	}
	return {
		fn1: fn1(),
		fn2: fn2()
	};
}());
```

外面就无法修改模块内部了，原因是自执行函数是一个表达式，而非定义或者声明方法，所以对象依然可以引用内部返回的结果，修改时却会报错。

起初，CommonJS由nodejs发扬光大，在其规范中，一个文件就是一个模块，独立的作用域。而在输出时，输出内容都是module.exports对象，我们只需要把要输出的内容，放入这个对象中。

加载模块使用require方法

```javascript
var test = require('./test.js');
```

但是这里有一个问题，这种规范是同步的，而在浏览器中文件是按顺序加载的，这是一个很尴尬的局面，导致在客户端是无法进行的，在服务器端就不存在这样的问题了。

于是，又引发出了AMD规范，异步模块化。原生的javascript是不支持AMD规范的，需要引入第三方库函数，比如RequireJS。

RequireJS主要解决了两个问题

- 一个是上面提到的异步，不影响浏览器的渲染

- 另一个是模块之间的依赖关系

定义模块test.js

```javascript
define([], function(){
	var name = 'Wellaiiyo';

	function print(){
		console.log(name);
	}

	return {
		print: print
	};
});
```

RequireJS定义了一个全局函数define来定义模块，参数数组为其要依赖的模块

加载模块

```javascript
require(['test'], function (_t){
	_t.print();
});
```

使用require来加载模块，参数中得数组为要加载的模块，第二个是回调函数，将加载的模块作为参数传递到回调内部使用。

国内阿里的SeaJS是CMD（通用模块化）规范，要解决的问题和RequireJS一样，不过在模块的定义和加载方式上不一样

定义模块test.js

```javascript
define(function(require, exports, module) {
  	var $ = require('jquery.js');
  	$('div').addClass('active');
});
```

加载模块

```javascript
seajs.use(['test.js'], function(_t){
	// code
});
```

### AMD和CMD的区别

- AMD推崇依赖前置，在定义模块的时候就要声明其依赖的模块
- CMD推崇就近依赖，只有在用到某个模块的时候再去require

最明显的地方在于，对依赖模块的执行时机处理不同，SeaJS加载模块其实也是异步的。AMD依赖前置，这样可以清晰的知道要依赖的模块，CMD是就近依赖原则，需要解析一边模块后才知道依赖哪些模块，看上去貌似后者用性能换取了开发的便捷，但是经过测试解析模块的时间是可以忽略的。AMD规范在模块加载后就会执行，这样模块的执行顺序和书写顺序不一定会一致，看网络速度。而CMD要等所有模块都加载完毕，进入主模块中的require时，才会执行相应的模块，这样模块的执行顺序和书写顺序就是一致的。

结果是：AMD用户体验好，没有延迟；CMD性能好，按需执行。
