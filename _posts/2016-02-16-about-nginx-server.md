---
layout: post
title: 'Nginx反向代理服务器'
date: 2016-02-23
---

Nginx是一个高性能的HTTP和反向代理服务器，也是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。

反向代理方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器。

为了在自己的一台服务器上配置多个站点，我使用了nginx反向代理来实现。

思路为：将所有该服务器下的域名指向服务器IP地址，由Nginx接收客户端的请求，然后再根据default.conf配置文件中的配置分发请求

	域 --> 服务器对应的端口服务

配置nginx下的default.conf文件
	
	server {
	    listen 80;
	    server_name  wellued.cn;

	    #charset koi8-r;
	    #access_log  /var/log/nginx/log/host.access.log  main;

	    location / {
	        proxy_pass http://127.0.0.1:3000;
	        #root   /root/Workspaces/WellUED;
	        #index  index.html index.htm;
	    }

	    #error_page  404              /404.html;

	    # redirect server error pages to the static page /50x.html
	    #
	    error_page   500 502 503 504  /50x.html;
	    location = /50x.html {
	        root   /usr/share/nginx/html;
	    }
	}
	server {
        listen 80;
        server_name chat.wellued.cn;

        location / {
                proxy_pass http://127.0.0.1:8001;
        }
	}
	server {
	        listen 80;
	        server_name devnote.wellued.cn;

	        location / {
	                proxy_pass http://127.0.0.1:4000;
	        }
	}

设置完nginx配置文件后，要重启nginx服务以使更新文件生效。

	$ nginx -s reload

> 遇上nginx这个坑起源于我捣腾自己的阿里云服务器，那段时光，生不如死！