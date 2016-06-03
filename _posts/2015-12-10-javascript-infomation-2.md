---
layout: post
title: "javascript代码杂谈（二）"
date: 2015-12-10
tags: "javascript"
---

HTMLElement原型拓展
HTMLElement.prototype

JavaScript基本类型：

- undefined
- boolean
- string
- number
- function
- object

Native

	var $ = function (id){
		return document.getElementById(id);
	};

	document.getElementsByTagName();
	document.getElementsByClassName();
	document.querySelector();	// IE8+
	document.querySelectorAll();	// IE8+

	el.parentElement;
	el.parentNode;
	el.lastChild;
	el.removeChild();
	el.textContent;

	el.innerHTML
	el.innerText

	document.createElement();
	el.appendChild();
	el.insertBefore();
	el.insertAfter();

	el.getAttribute();
	el.setAttribute();

	isArray
	indexof
	trim
	call()
	apply()
	bind()
	prototype
	__proto__
	currentTarget
	iframe.contentWindow 	// 获取子窗口的window对象
	iframe.contentDocument 	//获取子窗口的document对象  IE8+
	getComputedStyle

	innerHeight
	clientHeight
	scrollHeight

	- Event

	el.addEventListener
	el.attachEvent 	// IE
	el.removeEventListener

	Object.assign({}, defaultOpts, opts);

jQuery

	$.fn.css = function (){}
	$.fn.attr = function (){}
	$.fn.data = function (){}

	// 利用extend合并上述代码
	$.fn.extend({
		css: function (){},
		attr: function (){},
		data: function (){}
	});

	$(selector).on(eventName, eventHandler);
	$(selector).off(eventName, eventHandler);

	$(selector).trigger();

	$.contains(el, child);

jQuery and Native

	// jQuery
	$el.siblings();

	// Native
	[].filter.call(el.parentNode.children, function(child) {
	  return child !== el;
	});

- Previous elements

	// jQuery
	$el.prev();

	// Native
	el.previousElementSibling;

- Next elements

	// next
	$el.next();
	el.nextElementSibling;

- Add class
	
	// jQuery
	$el.addClass(className);

	// Native
	el.classList.add(className);

- Remove class
	
	// jQuery
	$el.removeClass(className);

	// Native
	el.classList.remove(className);

- Has class

	// jQuery
	$el.hasClass(className);

	// Native
	el.classList.contains(className);

- Toggle class

	// jQuery
	$el.toggleClass(className);

	// Native
	el.classList.toggle(className);

- Position

	// jQuery
	$el.position();

	// Native
	{ left: el.offsetLeft, top: el.offsetTop }

ECMAScript2015（ES6）

	let
	const

	=>
	`Hi, I'm ${name}, I am the ${title} at ${company}`
