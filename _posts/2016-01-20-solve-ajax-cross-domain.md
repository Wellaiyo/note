---
layout: post
title: '解决开发环境下ajax跨域问题的影响'
date: 2016-01-20
---

ajax是不支持跨域的，所以要解决跨域问题，要么修改header，要么用jsonp。

而我这边暂时只是想解决开发环境下ajax的跨域问题，所以使用了更为简单的办法。就是取消浏览器对ajax的跨域限制

很简单，Mac下完全退出Chrome浏览器，在终端输入下面的命令打开Chrome浏览器：

	open -a "Google Chrome" --args --disable-web-security

打开后浏览器会弹出一条提示，告诉你安全性降低了。这样就说明成功了
