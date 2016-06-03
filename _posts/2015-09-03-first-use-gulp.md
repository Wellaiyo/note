---
layout: post
title: 'Gulp — 前端自动化工作流'
date: 2015-09-03
---

用Gulp构建前端工自动化作流是目前最好的方式，我这么认为。

这里不会为Gulp作介绍，需要了解的移步[Gulp中文官网](http://www.gulpjs.com.cn/)。

Gulp工作流基于Nodejs，所以首先确定好自己的开发环境。

安装Gulp

	// 全局
	$ npm install -g gulp

	// 项目内
	$ npm install --save-dev gulp

Gulp提供五个方法接口来处理任务，你只需要懂得运用这五个方法，就能使用Gulp得心应手的解决问题。

- gulp.src()
- gulp.dest()
- gulp.task()
- gulp.run()
- gulp.watch()

.src用于产生数据流，参数表示要处理的文件

	js/test.js : 指定确切的文件名
	js/*.js : 某个目录所有后缀名为js的文件
	js/*/.js : 某个目录及其所有子目录中的所有后缀名为js的文件
	!js/test.js : 除了js/test.js以外的所有文件
	*.+(js|css) : 匹配项目根目录下，所有后缀名为js或css的文件

上述是.src参数的一般情况，除了那些外还能是一个数组

```javascript
gulp.src(['js/**/*.js', '!js/**/*.min.js']);
```

.dest用于传送文件，接收参数是文件的传送地址

```javascript
gulp.src('./src/js/*.js')
  .pipe(gulp.dest('./build/js'));
```

简单来说，.dest更像是将文件复制到项目中的某一位置，在应用中利用它可以将处理好的文件复制到指定位置。

.task用于定义任务，Gulp工作流是由大大小小的很多任务组成的，接收两个参数：任务名和任务方法

```javascript
gulp.task('test', function (){
	console.log('This is a test task');
});
```

在项目中我们一般这样使用
```javascript
gulp.task('build', ['css','js','images']);
```

如此便是定义了一个build任务，该任务由后面数组中的三个任务组成，因为每个任务都是回调执行，无法保证执行顺序。当然，如果写成依赖模式就能避免这样的问题

```javascript
gulp.task('js', ['css'], function (){
	console.log('js任务必须要等css任务加载执行完毕后才能执行');
});
```

.run执行任务。要注意的是，任务并不是按照指定顺序执行的，而是尽可能多的并行执行。那么这样容易引发一些问题，解决办法是：

- 自己写好依赖
- 利用插件
- Gulp v4.0

自己写依赖的话，会很麻烦，而且容易混乱，因为依赖与依赖之间可能会相互依赖。

4.0还没正式发布，所以也不靠谱，那么用插件来搞定是目前最好的方法了。[gulp-sequence](https://github.com/teambition/gulp-sequence)，来自teambition的解决方案。

.watch用来监听文件变动，当我们监听到文件变动时，就执行程序重新打包文件。

```javascript
gulp.watch('js/**/*.js', function(event){
	// do something
});
```

常用插件

- gulp-ruby-sass : 支持sass
- gulp-minify-css : 压缩css
- gulp-jshint : 检查js
- gulp-uglify : 压缩js
- gulp-concat : 合并文件
- gulp-rename : 重命名文件
- gulp-htmlmin : 压缩html
- gulp-clean : 清空文件夹
- browser-sync : 静态文件服务器，同时也支持浏览器自动刷新
- connect-history-api-fallback : 开发angular应用必须，用于支持HTML5 history API

一个完整的Demo

```javascript
var gulp = require('gulp'),            	       //基础库
    imagemin = require('gulp-imagemin'),       //图片压缩
    minifycss = require('gulp-minify-css'),    //css压缩
    uglify  = require('gulp-uglify'),          //js压缩
    rename = require('gulp-rename'),           //重命名
    concat = require('gulp-concat'),           //合并文件
	clean = require('gulp-clean');             //清空文件夹


// 样式处理
gulp.task('css', function () {
    var cssSrc = './public/css/*.css',
        cssDst = './build/css';
 
    gulp.src(cssSrc)
        .pipe(minifycss())
        .pipe(gulp.dest(cssDst));
});

// 图片处理
gulp.task('images', function(){
    var imgSrc = './public/images/**/*',
        imgDst = './build/images';
    gulp.src(imgSrc)
        .pipe(imagemin())
        .pipe(gulp.dest(imgDst));
})
 
// js处理
gulp.task('js', function () {
    var jsSrc = './public/js/*.js',
        jsDst ='./build/js';
 
    gulp.src(jsSrc)
        .pipe(concat('main.js'))
        .pipe(gulp.dest(jsDst))
        .pipe(rename({ suffix: '.min' }))
        .pipe(uglify())
        .pipe(gulp.dest(jsDst));
});
 
// 清空图片、样式、js
gulp.task('clean', function() {
    gulp.src(['./build/css', './build/js', './build/images'], {read: false})
        .pipe(clean());
});
 
// 默认任务 清空图片、样式、js并重建 运行语句 gulp
gulp.task('default', ['clean'], function(){
    gulp.start('css','images','js');
});

// 监听任务 运行语句 gulp watch
gulp.task('watch',function(){

    // 监听css
    gulp.watch('public/css/*.css', function(){
        gulp.start('css');
    });

    // 监听images
    gulp.watch('public/images/**/*', function(){
        gulp.start('images');
    });

    // 监听js
    gulp.watch('public/js/*.js', function(){
        gulp.start('js');
    });
});
```

相比于其它方式（比如Grunt），Gulp维护起来更像是写js代码，而且遵循CommonJS规范，和写Nodejs程序几乎每差异。Gulp没有中间件，减少了I/O压力。
