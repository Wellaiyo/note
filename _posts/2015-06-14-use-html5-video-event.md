---
layout: post
title: 'HTML5 Video事件控制'
date: 2015-06-14
---

随着浏览器版本的不断更新，html5应用越来越广，其中video算得上是一大利器。

最近公司要在产品中加一个视频播放，然后找了一些关于video的信息。第一个找到了的是video.js，在github上有比较多的星星，应该算是开源中最好的一款了。不过我们的应用还是比较简单的，所以我并没有打算再在项目中加入这个插件，而是选择自己写。

其实难度几乎是没有的，主要问题还是在于对video的控制器的设定，video自带的controls有点丑。

### HTML5 vidoe 基础标签

	<video id="test_video" poster="test_video.jpg" width="600" height="400" autoplay controls>
		<source src="test_video.mp4" type="video/mp4" />
		<source src="test_video.webm" type="video/webM" />
		<source src="test_video.ogv" type="video/ogg" />
		<p>Your browser does not support the video tag.</p>
	</video>

mp4, webm, ogv三种视频源是为了兼容不同浏览器的，如果你的浏览器不兼容video标签，会显示p标签中的文字，用作提示。

html5 video 有一系列的api可以用javascript访问，这就是我们设定video controls的基础。

	var _v = document.getElementById('test_video')

接下来就是重要部分了

### html5 video的播放与暂停

```javascript
$('#playBtn').on('click', function (){
	if(_v[0].paused){
		_v.paly();
	}else {
		_v.pause();
	}
	return false;
});
```

html5的视频支持回放，所以我们可以自定义视频的总时间和当前播放时间，要获取总时间，那么要保证视频加载完毕，我们需要使用video的loadedmetadata事件。

当前的时间可以用timeupdate事件来保证他的更新。

```javascript
_v.on('loadedmetadata', function() {
	$('#zongshijian').text(_v[0].duration);
});

_v.on('timeupdate', function() {
	$('#dangqianshijian').text(_v[0].currentTime);
});
```

如果只是播放时间的控制明显不够人性化，那么加上进度条就好多了。

```javascript
_v.on('loadedmetadata', function() {
	$('#zongshijian').text(_v[0].duration));
});
 
_v.on('timeupdate', function() {
   	var currentPos = _v[0].currentTime,
		maxduration = _v[0].duration,
   		percentage = 100 * currentPos / maxduration;

   	$('#jindutiao').css('width', percentage+'%');
});
```

进阶下~

```javascript
var timeDrag = false;
$('.progressBar').mousedown(function(e) {
	timeDrag = true;
	updatebar(e.pageX);
});
$(document).mouseup(function(e) {
   	if(timeDrag) {
		timeDrag = false;
		updatebar(e.pageX);
   	}
});
$(document).mousemove(function(e) {
   	if(timeDrag) {
		updatebar(e.pageX);
   	}
});

var updatebar = function(x) {
		var progress = $('.progressBar'),
			maxduration = _v[0].duration,
			position = x - progress.offset().left,
			percentage = 100 * position / progress.width();

		if(percentage > 100) {
  		percentage = 100;
		}
		if(percentage < 0) {
  		percentage = 0;
		}

		$('#jindutiao').css('width', percentage+'%');
		_v[0].currentTime = maxduration * percentage / 100;
};
```

### 全屏播放控制

```javascript
$('#fullscreen').on('click', function (){
	_v[0].webkitEnterFullscreen();
	_v[0].mozRequestFullScreen();
		return false;
});
```

webkit开头是支持webkit内核的浏览器，moz是支持Firefox，这应该很简单。

更多API请Google，能找到的资料简直不要太多~
