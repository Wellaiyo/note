---
layout: post
title: '初识React'
date: 2015-04-17
tags: 'react'
---

要说目前前端er们都在讨论的热点，绝对应该算上 [React](http://facebook.github.io/react/) 。虚拟DOM、组件化开发方式、高性能让大家都不禁向react投来深切的关注。

react起源于Facebook内部的学习小组F8，应该团队认为就目前的某些项目来说，MVC框架模式并非是最好的选择。于是自己写了一套用于网站使用，发现很好用于是就开源了。由于react的设计思想极其独特，属于革命性创新，性能出众，代码逻辑却非常简单。所以，越来越多的人开始关注和使用，认为它可能是将来 Web 开发的主流工具。

react不是一个MVC框架，不过倒是挺像MVC中的V（view），虚拟DOM为react带来了高性能，尽管关于react的虚拟DOM是否真的高性能，仍有人对此有质疑。

react的虚拟DOM是依赖一套javascriptDOM API在浏览器构建DOM结构的，每次数据刷新时，整个虚拟DOM数都要重构。react会对比当前DOM树与上一次DOM树的区别在哪里，然后将需要改变的部分进行实际的浏览器DOM更新`（DOM diff）`。因为虚拟DOM是内存数据，所以性能是极高的。如此一来，开发者将不再需要关注某个数据的变化如何更新到一个或多个具体的DOM元素，而只需要关心在任意一个数据状态下，整个界面是如何Render的。

react的组件化思想认为，整个UI都以组件构成，每个组建都是具有独立功能的UI部件，然后无数的小组件可以构成大的组件。也就是说，如果你用react构建一个项目，应该把整个项目的UI看做是一个大的组件，由无数的小组件组成，组件中嵌套组件。

举个简单的例子：评论功能

<img src="/images/articles/first-use-react.jpg" alt="" />

该组件化设计思想中认为，一个组件应该具备以下特征

- <b>可组合</b>：一个组件易于和其它组件一起使用，如果一个组件内部创建了另一个组件，那么父组件拥有它创建的子组件。
- <b>可重用</b>：组件可独立，并在多个UI场景随意使用。
- <b>可维护</b>：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护。

在实际的react开发中，推荐代码编写并不是用javascript编写的，而是用语法糖编写的，比如JSX就是一种。并不是说react一定要用JSX才能开发，脱离JSX react一样可以工作。只是使用了JSX让代码结构看上去更为清晰，也提高了开发效率。

因为其不与javascript兼容，所以我们要将JSX语法转换成javascript语法，这一步是很消耗时间的，所以在实际上线的时候，应该把这一步放在服务器执行。

新版中已经移除了JSXTransformer.js，所以后续的JSX开发都要依赖于browser.js

```html
<script src="lib/react.js"></script>
<script src="lib/react-dom.js"></script>
<script src="lib/browser.min.js"></script>

<div id="example"></div>
<script type="text/babel">
	ReactDOM.render(
		<div>Hello Wiley!</div>,
		document.getElementById('example')
	);
</script>
```

值得注意的是，render的第二个参数必须是原生JS获取DOM节点的方法，而不能使用jQuery方法，当然并不是说在项目中不能使用jQuery。

JSX语法允许javascript和html混写，不需要加引号，这种情况有点类似于AngularJS的写法。

JSX 允许直接在模板插入javascript变量。如果这个变量是一个数组，则会展开这个数组的所有成员。

```javascript
var testArr = ['Wiley','Wellaiyo'];
ReactDOM.render(
	<div>{
		testArr.map(function (info){
			return <h1>Hello, {info}</h1>;
		})
	}</div>,
	document.getElementById('example')
);
```

创建组件

```javascript
var Greet = React.createClass({
	render: function (){
		return <h1>Hello, {this.props.name}</h1>;
	}
});
ReactDOM.render(
	<Greet name="Jeck" />,
	document.getElementById('example')
)
```

组件的属性可以在组件类的this.props对象上获取。要注意的是，组件的第一个字母要大写，不然会报错。

添加组件属性，有一个地方需要注意，就是 class 属性需要写成 className ，for 属性需要写成 htmlFor ，这是因为 class 和 for 是 JavaScript 的保留字。

this.props对象的属性与组件的属性一一对应，除了this.props.children属性，它表示组件的所有子节点。

```javascript
var NotesList = React.createClass({
		render: function() {
		return (
			<ol>
				{
        			React.Children.map(this.props.children, function (child) {
          				return <li>{child}</li>;
        			})
      			}
      		</ol>
    	);
  	}
});

ReactDOM.render(
	<NotesList>
    	<span>hello</span>
    	<span>world</span>
  	</NotesList>,
  	document.getElementById('example')
);
```

组件毕竟是虚拟DOM，要获取真实DOM需要用到ref属性

```javascript
var Component = React.createClass({
	print: function (){
		console.log(this.refs.testTxt.value);
	},
	render: function (){
		return (
			<div>
				<input type="text" ref="testTxt" />
				<input type="button" value="print" onClick={this.print} />
			</div>
		);
	}
});

ReactDOM.render(
	<Component />,
	document.getElementById('example')
);
```

组件是与用户交互的重心，所以组件的状态时常会发生变化。react认为组件可以是一个状态机，具备初始状态，与用户互动状态就改变，从而触发重新渲染UI。

```javascript
var Component = React.createClass({
	getInitialState: function (){
		return {bool: false};
	},
	changeState: function (){
		this.setState({bool: !this.state.bool});
	},
	render: function (){
		return (
			<div>
				<input type="text" value={this.state.bool} />
				<input type="button" value="changeState" onClick={this.changeState} />
			</div>
		);
	}
});

ReactDOM.render(
	<Component />,
	document.getElementById('example')
);
```

react事件绑定和原生的js事件绑定是不同的，因为react事件绑定在reactDOM上。下面是一个完整的例子

```javascript
var TestButton = React.createClass({
	handler: function (event){
		var tipEle =  ReactDOM.findDOMNode(this.refs.tip);
		if(tipEle.style.display == 'none'){
			tipEle.style.display = 'inline';
		}else {
			tipEle.style.display = 'none';
		}

		event.stopPropagation();
		event.preventDefault();
	},
	render: function (){
		return (
			<div>
				<button onClick={this.handler}>test</button><span ref="tip">测试点击</span>
			</div>
		);
	}
});
var TestInput = React.createClass({
	getInitialState: function (){
		return {
			inputContent: ''
		};
	},
	changeHandler: function (event){
		this.setState({
			inputContent: event.target.value
		});

		event.stopPropagation();
		event.preventDefault();
	},
	render: function (){
		return (
			<div>
				<input onChange={this.changeHandler} type="text" /><span>{ this.state.inputContent }</span>
			</div>
		);
	}
})
ReactDOM.render(
	<div>
		<TestButton />
		<br />
		<TestInput />
	</div>,
	document.getElementById('example')
);
```

在我的理解中，组件中的内容都是用变量属性来代替的，DOM操作改变内容改变的是状态，也就是前面说的，react组件是一个状态机。还有一点值得注意的是，react事件中的参数event和原生中的event不同，是在原生中的event基础上进行了封装，也就是说react中的event除了带有原生自带的event属性功能还有react自有的属性功能。比如上述例子中就用代码取消了原生自带的属性功能。

	event.stopPropagation();
	event.preventDefault();

深入认识react，那必须要了解组件的生命周期

- Mounted 已插入真实DOM
- Update 正在被重新渲染
- Unmounted 已移除真实DOM

这其中还包括will和did，就是每个渲染步骤前和每个渲染步骤后

	componentWillMount
	componentDidMount

	componentWillUpdate
	componentDidUpdate
	// render 过程
	componentWillReceiveProps
	shouldComponentUpdate
	// 修改state操作

	componentWillUnmount

componentWillUnmount是将一个react Component销毁的阶段，释放内存资源、图片资源等操作，但是因为浏览器的垃圾回收机制，这个属性一般很少被使用。

~~

~~

更多资料，请参考 [React官方文档](http://facebook.github.io/react/docs)

感谢 [阮一峰](http://www.ruanyifeng.com/blog/2015/03/react.html) 前辈的精彩博文
