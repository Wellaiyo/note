---
layout: post
title: '如何编写一个Chrome扩展'
date: 2015-12-17
---

Chrome的V8引擎出来以后，javascript再也不仅仅只是一个简单的web脚本语言了，最为突出的一个特点便是我们所熟悉的Nodejs，基于V8引擎进行解析。还有一个很有意思的玩法，就是开发Chrome扩展小程序。

Chrome扩展程序的界面是基于html来编写的，也就是我们正常开发网页是一样的，这对于前端工程师来说，实在有点轻松。

所以，基础的文件结构是：

- manifest.json
- popup.html
- js/
- images/

开发测试准备工作如下，将项目文件加载到Chrome扩展

<img src="/images/articles/chrome-extend.png" alt="" />

manifest.json是配置文件，Chrome浏览器加载扩展程序的时候，会先寻找该文件，可以说，manifest.json是整个程序的一个入口。

	{
		"manifest_version": 2,	// manifest的版本
		"name": "Clock",		// 扩展的名称
		"author": "Wellaiyo",
		"version": "1.0",	// 扩展的版本号
		"description": "Chrome扩展小应用，时钟",	// 扩展的描述
		"icons": {		// 图标
			"16": "images/icon16.png",
			"48": "images/icon48.png",
			"128": "images/icon128.png"
		},
		"browser_action": {
	        	"default_icon": {		// 扩展图标在工具栏中的位置
        	    		"19": "images/icon19.png",	// 正常显示
            	    	 	"38": "images/icon38.png"	// 视网膜屏幕下显示
        		},
        		"default_title": "时钟",		// 鼠标移动到扩展图标上显示的文字
        		"default_popup": "popup.html"	// 点击扩展图标显示的界面
    		}
	}

这是一个简单的manifest.json

从上述代码中，我们可以看到popup.html，这便是指向我们扩展程序界面的代码文件。

	<html>
	<head>
		<style>
			body, body * {
				margin:0;
				padding:0;
			}
			body {
				width:160px;
				height:60px;
			}
			#clock {
				text-align:center;
				font-size:24px;
				line-height:60px;
			}
		</style>
	</head>
	<body>
		<div id="clock"></div>
		<script src="js/clock.js"></script>
	</body>
	</html>

可以看到的是，这个html文件中省略了很多东西，比如<!doctype html>，为什么不需要声明呢？首先扩展程序并不是网页程序，它虽然是用html编写的，但因为不需要遵循任何协议标准，所以像web的开头声明在Chrome扩展程序中是没有任何意义的。

还有就是title标签，试过效果之后你就会明白为什么不需要这个标签。当然，如果你的程序中有中文的话，编码格式utf8还是要的。

Chrome不允许将JavaScript代码段直接内嵌入HTML文档，所以我们需要通过外部引入的方式引用JS文件。

### 操作用户正在浏览的页面

通过配置manifest.json文件，还可以让扩展程序做更多的事情，比如操作用户正在浏览的页面。

这个是利用content_script属性来完成的。

	"content_scripts": [{
		"matches": ["*://wellued.cn/"],
		"js": ["js/app.js"]
	}]

也许，你已经开始YY了，这可能会造成网页的安全性问题吧。

这种情况当然是不会存在的，你注入的js脚本，只能操作DOM，无法干扰到网页内嵌的js脚本，所以关于安全性的YY完全可以停止了。

不过这就已经够我们做很多事情了，比如用户访问淘宝，你可以监听用户是否查看的是详情页面 `matches` ，然后给加入购物车这个按钮也增加一个事件，点击的时候将数据保存起来。这样就形成了一个购物记录的应用了。

### 跨域请求

我们知道，一般来说浏览器是不允许跨域的，因为一系列的安全性考虑。这样的一条规则极大的限制了Chrome扩展程序的能力，所以Google允许Chrome扩展程序跨域请求，但要在manifest.json文件中配置permissions属性声明需要跨域的权限。

	"permissions": [
    		"*://*.wellued.cn/*"
	]

这样，扩展程序就可以在任意页面请求该域下的资源了。

顺便提一下，跨域js函数

	function httpRequest(url, callback){
    		var xhr = new XMLHttpRequest();
    		xhr.open("GET", url, true);
   		xhr.onreadystatechange = function() {
        		if(xhr.readyState == 4) {
            			callback(xhr.responseText);
        		}
    		}
    		xhr.send();
	}

这是一个异步函数，为了减少程序阻塞所带来的困扰。

### 后台监听

每次调用都得用户手动点击，这明显是一种很笨的方法，我们希望用户安装了该扩展程序后，该程序就一直后台监控执行。

Chrome扩展程序也是允许这样做的，而且当后台检测到状态发生改变的时候，也会通知UI界面刷新。

这依赖于manifest.json中的background属性，background属性包含3个子属性：`scripts`、`page`、`persistent`，指定了scripts属性，那么Chrome扩展在启动的时候，会创建一个包含所有指定脚本的页面；如果指定了page属性，则将指定的page页面作为后台html页面运行；persistent属性定义了其方式，true为一直运行，false为按需运行，显然一直运行是很浪费性能资源的。persistent属性的默认值是true，所以在配置的时候要将persistent设置为false。

	"background": {
    		"scripts": [
        		"js/status.js"
    		],
    		"persistent": false
	}

一般来说我们只需要后台运行的脚本程序，而不需要特定的UI界面，所以只需要配置了scripts属性就OK了。

### 数据存储

一般来说，用户在网站中的很多数据都会存储在Cookie中，或者是网站的服务器数据库中。而存储在javascript变量中的话，在关闭浏览器的时候，这些数据就会消失。

好在html5提供了localStorage接口，允许JavaScript在用户计算机硬盘上永久储存数据。不过它也有一些限制，首先是类似Cookie一样，受域的限制，不允许跨域调用。其次是单个域中最大只能储存5MB大小的数据。最后localStorage只能储存字符串型的数据，无法保存数组和对象，但可以通过join、toString和JSON.stringify等方法先转换成字符串再储存。

### 开启选项

鼠标右键点击扩展图标的时候，会弹出一些选项，其中有一个名叫“选项”的选项，它是灰色的（无法点击）。有时候我们需要为扩展程序做一些个性化的设置等等，所以会需要开启选项这个功能。

实现很简单，manifest.json配置中增加一个 options_page ，它会指定一个页面用来做扩展的选项设置页面。

	"options_page": "options.html"

Chrome提供的大部分API是不支持在content_scripts中运行的，但runtime.sendMessage和runtime.onMessage可以在content_scripts中运行，所以扩展的其他页面也可以同content_scripts相互通信。

runtime.sendMessage和runtime.onMessage是扩展页面之间的通信接口，使用方法为


	chrome.runtime.sendMessage(extensionId, message, options, callback);

其中extensionId为所发送消息的目标扩展，如果不指定这个值，则默认为发起此消息的扩展本身；message为要发送的内容，类型随意，内容随意，可以是字符串、数组、JSON等等。options为对象类型，包含一个值为布尔型的includeTlsChannelId属性（仅在扩展和网页间通信时才会用到），此属性的值决定扩展发起此消息时是否要将TLS通道ID发送给监听此消息的外部扩展。

	chrome.runtime.onMessage.addListener(callback);

onMessage中的callback接收到的参数有三个，分别是message、sender和sendResponse，即消息内容、消息发送者相关信息和相应函数

回到UI界面，关于图标，Chrome扩展也是可以灵活设置的，通过setIcon方法可以动态更改扩展的图标。另外，如果配置文件中的 default_icon 没有指定的话，那么Chrome会使用一个默认的图标。

	chrome.browserAction.setIcon(details, callback);

其中details的类型为对象，可以包含三个属性，分别是imageData、path和tabId。

这里主要讲下path属性，path的值可以是字符串，也可以是对象。如果是对象，结构为{size: imagePath}。imagePath为图片在扩展根目录下的相对位置。

	chrome.browserAction.setIcon({path: {'19': 'images/icon19_new.png'}});
	chrome.browserAction.setIcon({path: {'38': 'images/icon38_new.png'}});

当鼠标移入扩展图标上时，显示的文字也是可以动态切换的，Chrome扩展提供了一个接口

	chrome.browserAction.setTitle({title: 'This is a new title'});

同时，修改badge也是被允许的。badge是一种为用户提供有限信息的方式，不过只能存入4个字节长度的内容。所以，一般应用于状态等信息的显示。

	chrome.browserAction.setBadgeBackgroundColor({color: '#0000FF'});
	chrome.browserAction.setBadgeText({text: 'Dog'});

### 右键菜单

	"permissions": [
    		"contextMenus"
	]

添加右键菜单也是比较简单的，加入上述代码同时确保icons中声明了16像素的图标。

Chrome提供了三种方法操作右键菜单，分别是create、update和remove，对应于创建、更新和移除操作。

	chrome.contextMenus.create({
    		type: 'normal',
    		title: 'Menu A',
    		id: 'a'
	});
	chrome.contextMenus.create({
    		type: 'radio',
    		title: 'Menu B',
    		id: 'b',
    		checked: true
	});
	chrome.contextMenus.create({
    		type: 'checkbox',
    		title: 'Menu C',
    		id: 'c',
    		checked: true
	});
	chrome.contextMenus.create({
   		type: 'normal',
    		title: 'Menu D',
    		id: 'd',
    		parentId: 'a'
	});

### 桌面提醒

利用webkitNotifications，不过大多数浏览器都不支持该属性，即使是chrome也要最新的浏览器版本才支持。

	if(!webkitNotifications.createNotification){
		alert ('您的浏览器不支持Notifications');
	}

上述代码是用来测试浏览器是否支持该API。

说了这么多，也只是大概了解一下Chrome扩展。想要更多的提高，大量的实践经验是必不可少的。还是那句话，<span style="color:#f60;">No pain no gain</span>

这里有一个最简单的 [Demo](https://github.com/Wellaiyo/Clock)

> 参考文档：[Chrome扩展及应用开发](http://www.ituring.com.cn/article/60223)
