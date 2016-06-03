---
layout: post
title: 'Socket.io开发实时聊天应用'
date: 2015-12-19
tags: 'javascript'
---

关于web实时推送、更新，我想大家首先想到的都会是[Socket.io](http://socket.io)。在Websocket之前，实现实时推送通常采用的是轮询，但这种方案有个明显的缺陷，需要由客户端向服务器发送大量请求寻找是否有新资源，想想都觉得有点邪门。先对WebSocket有个基础的了解，可以查下这篇文章[WebSocket原理解析](http://wellued.cn/blog/2015/11/WebSocket%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90)。

为了对socket.io有个深入的了解，我实战了一个小项目，一个简单的实时聊天应用。

Socket.IO是一个开源的WebSocket库，它通过Node.js实现WebSocket服务端通信，同时也提供客户端JS库用于前端的对接。

所以要先有node环境，安装nodejs可以去[Nodejs官网](http://www.nodejs.org/download)。如果安装成功，在命令行输入`node -v`和`npm -v`可以查看到版本号。

	$ node -v
	$ npm -v

因为我这边的nodejs应用是基于express搭建的，所以还要安装express

	$ npm install --save express

用express开启应用后，接下来就是安装socket.io模块了

	$ npm install --save socket.io

OK，到这里开发环境就准备的差不多了。

socket.io实现了端与端之间的通信，加载了socket.io模块后，就开启一个socket服务了。

	var server = require('http').cerateServer();
	var io = require('socket.io')(server);

	server.listen(8000);

	io.on('connection', function(socket){
		socket.on('event', function(data){});
		socket.on('disconnect', function(){});
	});

socket.io提供了一些列的方法用来操作消息进行通信

	socket.emit()
	socket.broadcast.emit()
	socket.on()

上述3个事件是最常用的事件，`.emit()`是发出消息，有两个参数，第一个参数是通信之间约定的名字，比如你要给你的某个朋友打电话，你得要一个电话号码，不然通信公司无法帮你找到对方。第二个参数是发送出去的数据，可以是字符串、数组等等。

	socket.emit('test1', 'data');

`.broadcast.emit()`和`.emit()`用法一样，不同的是前者是向除了自己意外的所有用户发送消息，而后者只向自己发送消息。比如（server发送给自己的客户端就用.emit()，发送给多有的客户端就用.broadcast.emit()）。

	socket.broadcast.emit('test2', 'data');

`.on()`是用来接收前两个事件发送出去的消息，它有两个参数，第一个是约定好的名称，第二个是一个回调函数，回调函数接收的一个参数就是发送过来的数据

	socket.on('test1', function (data){
		console.log(data);
	});

而要与前端进行通讯就需要socket.io官方提供的一个js文件

	<script src="socket.io.js"></script>

同样的事件，在前后端一样的使用，一样的意义。这样看起来，使用方法就清明多了。

现在来整理下聊天项目的思路：

- 用户在前端页面中输入内容，js获取内容信息、发送人。用.emit()发送给后端，后端接收到再广播出去，.broadcast.emit()

因为后端为了分辨谁是谁，所以一般给socket加上id用来区分，比如我这边会将username绑定到socket上

	socket.username = username;

在前端js代码中也会用.on()接收后端广播出来的消息，这样，消息发送、接收就耦合到一起了。

然后是连接数据库，因为要保存聊天记录，你总不会希望你一关闭网页之前聊的内容就全部丢失了吧。

所以我们创建的消息要add道数据库中，我这边使用的是mysql，在nodejs中加载了一个mysql模块，操作起来非常的方便。

	$ npm install mysql

建立一个connection连接上mysql数据库，接下来就是让里面存数据了。

如果要让所有人刚刚连上socket就能看到聊天记录的话，在页面加载的时候或者说socket刚连接上从数据库获取数据，然后展示在前端。这个可以用你觉得好的任何方式，我这边直接用了socket事件操作。

OK，这样一来，整个项目就差不多了。剩下的无非就是一些细节或者说是补充性的工作了，我想这对于一个前端工程师来说，再简单、熟练不过了。

深入了解Socket.io请移步[socket.io官网](http://socket.io)

这里有一个小[Demo](http://chat.wellued.cn)，源码在[Github](https://github.com/Wellaiyo/Chat)上

> 客官，测试的时候还请温柔点！
