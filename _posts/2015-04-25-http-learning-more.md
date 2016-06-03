---
layout: post
title: '熟悉HTTP协议'
date: 2015-04-25
---

HTTP是超文本传输协议（请求/响应模式），于90年提出，经过几年的发展和完善，目前已越来越规范。

HTTP协议的主要特点：

- 支持客户端/服务端模式
- HTTP协议简单，使得相应的程序规模小，通信快速
- 允许传输任意类型的数据对象
- HTTP每次连接都只处理一个请求，建立连接->开始请求->请求结束->断开连接
- 无状态协议，这意味着每一个请求都是相互独立的

HTTP URL格式：

	http://[host]:[port][abs_path]

http表示通过HTTP协议来定位网络资源，host指明主机地址，port指定一个端口号，为空则默认80端口，abs_path说明了请求资源的URI。未指明URI时，必须必 '/' 结束，一般浏览器会自动完成这个工作。

http请求分为三部分：请求行、消息报头、请求正文

请求的方法有：

- GET  请求获取Request-URI所标识的资源
- POST  在Request-URI所标识的资源后附加新的数据
- HEAD  请求获取由Request-URI所标识的资源的响应消息报头
- PUT  请求服务器存储一个资源，并用Request-URI作为其标识
- DELETE  请求服务器删除Request-URI所标识的资源
- TRACE  请求服务器回送收到的请求信息，主要用于测试或诊断
- OPTIONS  请求查询服务器的性能，或者查询与资源相关的选项和需求
- CONNECT  保留将来使用

POST方法要求被请求的服务器接受附在请求后的数据，也就是我们所要提交给服务器的数据

在服务器拿到并理解了客户端的请求后，服务器返回一个HTTP响应消息。

同样分为三部分：状态行、消息报头、响应正文

状态行格式如下：

	HTTP-Version Status-Code Reason-Phrase CRLF

HTTP-Version表示服务器HTTP协议的版本；Status-Code表示服务器发回的响应状态代码；Reason-Phrase表示状态代码的文本描述。

状态码有3个数字组成，第一个数字定义了响应的类别。

- 1xx：指示信息--表示请求已接收，继续处理
- 2xx：成功--表示请求已被成功接收、理解、接受
- 3xx：重定向--要完成请求必须进行更进一步的操作
- 4xx：客户端错误--请求有语法错误或请求无法实现
- 5xx：服务器端错误--服务器未能实现合法的请求

常见状态码如下：

- 200 OK      //客户端请求成功
- 400 Bad Request  //客户端请求有语法错误，不能被服务器所理解
- 401 Unauthorized //请求未经授权，这个状态代码必须和WWWAuthenticate报头域一起使用 
- 403 Forbidden  //服务器收到请求，但是拒绝提供服务
- 404 Not Found  //请求资源不存在，eg：输入了错误的URL
- 500 Internal Server Error //服务器发生不可预期的错误
- 503 Server Unavailable  //服务器当前不能处理客户端的请求，一段时间后可能恢复正常

HTTP消息报头包括普通报头、请求报头、响应报头、实体报头。每一个报头域都是由名字+“：”+空格+值 组成，消息报头域的名字是大小写无关的。

Cache-Control   用于指定缓存指令，缓存指令是单向的（响应中出现的缓存指令在请求中未必会出现），且是独立的（一个消息的缓存指令不会影响另一个消息处理的缓存机制），HTTP1.0使用的类似的报头域为Pragma。

only-if-cached   响应时的缓存指令包括：public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age、s-maxage。

