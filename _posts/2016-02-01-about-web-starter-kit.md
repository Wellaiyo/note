---
layout: post
title: '前端神器—Web Starter Kit'
date: 2016-02-01
---

为什么我爱上了web starter kit？

作为Google的前端开发规范，web starter kit可以算得上是前端项目开发中最为通用的最佳实践了，它包含了本地服务，项目结构初始化，打包发布等一系列作用。是一个简洁却功能强大的前端工程化模板。

## 特性

- 开发前标准化项目基础
- 开发时实时刷新、多终端同步
- 开发后快速打包

## 安装说明

基于 nodejs, cnpm（npm可能会存在某些module安装失败的情况）

全局安装 Gulp

	$ cnpm install --global gulp

安装项目模块

	$ cnpm install

## 开始使用

移除 `README.md` 文件，并在 app/ 文件夹下开发你的项目

若项目中的页面相互独立，使用 index.html，删除 basic.html

若项目中的页面依赖于 layout ，使用 basic.html，删除 index.html

多终端同步：获取你本机的IP，并在各终端上打开该IP地址，端口为你项目本地服务的端口号

## 使用命令行

开启本地服务

	$ gulp serve

打包发布

	$ gulp

js打包方式为在html中的js引用前后添加相应的注释代码

	<!-- build:js(app/) /scripts/main.min.js -->
	<script src="./scripts/main.js"></script>
	<!-- endbuild -->

## 注意事项

- 代码缩进统一为4个空格
- 不要为项目开多个窗口，因为实时刷新事件，容易误导你以为你的程序出了BUG
