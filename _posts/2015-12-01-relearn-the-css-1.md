---
layout: post
title: "重新认识CSS（一）"
date: 2015-12-01
tags: "css"
---

> 面试了几个大公司，很多基础的问题都没有答出来，这让我很是尴尬、羞愧。于是便记录下了这次的面试总结，原因无非就是学习的时候心态过于浮躁，心高手底导致基础底子薄弱。那OK，意识到了问题所在就好解决了，先从css开始。写下一系列文章记录我重新学习css的点点滴滴，同时也当作是备忘录吧。

感谢张鑫旭前辈的[博文](http://www.zhangxinxu.com/wordpress/2012/07/bottleneck-css-study/)，提醒了我，我现在应该是出于学习中的又一个瓶颈了。学的东西比较杂，但是没几个有过深入的研究，导致现在不上不下的尴尬局面。

首先我们来看，绝对定位和相对定位的实现？

我们都知道，定位（position）有四个属性值，静态、绝对、相对和固定。

- 静态（static）：一般元素的默认状态，不能通过z-index进行层次分级
- 相对（relative）：不脱离文档流，参考自身静态位置可用left\top\right\bottom定位，可层次分级
- 绝对（absolute）：脱离文档流，选取最近的一个有定位设置（relative、absolute、fixed）的父级通过left\top\right\bottom定位，可层次分级
- 固定（fixed）：脱离文档流，相对于视窗定位，可层次分级

这是一个比较基础的知识点，某公司的面试官就问到我一个相关的问题，不过仔细想想，以前确实没有注意到这其中的种种，直接忽视了，而这存在后续开发中若出现相关的但不常见的问题时会有无从下手的风险。比如，你以为absolute仅仅是相对于relative定位？不，它可以相对于relative、absolute、fixed任一定位。

那么我们都知道，css布局是基于盒模型来处理的，盒模型的相关种种都是些什么呢？

抛开css3来说，盒模型一般存在两种模式，一种是标准模式，另一种是怪异模式。

html文档的开头会有一个

```htlm
<!DOCTYPE HTML>

<!DOCTYPE html PUBLIC ~~~~~~~ >
```

有这种声明的会触发标准模式，而缺失声明在IE6、IE7、IE8下会触发一种怪异模式（quirk mode）。例如

```css
.box {
	width:200px;
	height:200px;
	padding:30px;
	margin:20px;
	border:10px solid black;
}
```

标准模式下，设定的宽高为内容区content的宽高，而整个盒子的宽高则是内容区的宽高加上相对平行位置上的padding、margin和border的值。

<img src="/images/articles/relearn-the-css-1-1.png" alt="" />

怪异模式下，设定的宽高为内容宽高、padding、margin和border的总和，和标准模式正好相反。巧妙的是，在IE6及以下版本中，也一直都是遵循的这种怪异模式。

那么在css3中，我们有了更多的选择权利，我们可以通过box-sizing来设置盒模型的计算方式：

- box-sizing:content-box; 采用标准模型解析计算
- box-sizing:border-box; 采用怪异模式解析计算

当然，目前使用此属性还要加上前缀

```css
-webkit-box-sizing:content-box;
-moz-box-sizing:content-box;
```

一般开发者对怪异模式都是避之不及，但了解bootstrap的开发者可能知道，BS框架从3.0版本开始就全面使用border-box了。那么我的想法是，在老一辈的浏览器们逐渐被淘汰的情况下，border-box应该被慢慢重视起来，并正确使用。BS框架使用border-box的原因，无非是基于灵活、各种自适应的设计原则，怪异模式无疑是更好的选择。

ul、ol、dl的区别？

这个也是以前忽略掉的一个点，ul是无序列表，ol是有序列表，dl是定义列表（默认前后层级关系dt-->dd）。被提到的时候大多会关联上语义化这个词。

它们在不同浏览器中是存在差异的，具体关系到IE6、7等低版本浏览器，我个人对低版本浏览器是比较排斥的，所以这里就不细谈了。有兴趣的可以百度 “列表元素ul、ol、dl的研究”。

display的相关研究？

这个属性应用场景比较多，比如常见的图片水平垂直居中技术，有很多方法：负margin、absolute center、table-cell等等，这里来看下table-cell

```css
.box1 {
	width:300px;
	height:300px;
	padding:10px;
	margin:20px;
	border:10px solid black;
	display:table-cell;
	vertical-align:middle;
	text-align:center;
}
.box1 img {
	width:270px;
}
```
```html
<div class="box1">
	<img src="relearn-the-css-1-2.png" alt="" />
</div>
```

这样，图片就能达到以下的效果

<img src="/images/articles/relearn-the-css-1-2.png" alt="" />

当然IE6、7不支持这种方法，但孤傲的我直接忽视了它们，即使存在其它解决办法。

table-cell还可以实现两栏自适应布局，具体只需要让其中一栏float，另一栏table-cell就OK了，不过该死的IE6、7大家别忘了，加上*display:inline-block搞定它。（提一下，属性前面加*是css hack，作用于IE6、7）

<img src="/images/articles/relearn-the-css-1-3.png" alt="" />

另外还有等高布局，也可以用table-cell来做（每个需要等高的元素都设置table-cell）。

仔细一想table-cell还可以用来做列表布局，真是个强大的玩意儿！

display比较浅显的就是，inline、inline-block、block

设置inline的元素为行内元素，设置为block的元素为块级元素，而inline-block正好取两者之优，既可以在行内显示，又可以设置宽高。

刚才有提到列表布局，OK，来聊下列表布局。

我们认知做多的就是利用浮动来做列表布局，但这有一个很大的限制，就是不支持不定高的列表布局（这里的不定高是指浮动元素）。当然我们有更多更好的方法来处理列表布局，比如：display:inline-block

嗯，兼容该死的IE6、7的话，加一句代码

```css
display:inline;
```

display:inline-block会引发出一个小问题，就是两个并列的元素之间会有间隙，解决办法是

```css
letter-spacing: -3px;
font-size: 0;
```

单独的font-size:0在chrome下无效，单独的letter-spacing负值在opera下无效。

我觉得张鑫旭前辈有一句话说的特别好：<span style="color:#f60">要主动驾驭CSS，而不是被CSS调试，被浏览器劫持</span>
