# 纯CSS实现浮动居中 #
问题描述：dom结构如下
```
<div style="width:100%">
	<ul>
		<li>我是一个宽度固定的li</li>
		<li>我是一个宽度固定的li</li>
		<li>我是一个宽度固定的li</li>
		...
		<li>我是一个宽度固定的li</li>
	</ul>
</div>
```
其中li为定宽的浮动元素，ul宽度自适应，最大为100%，li个数不定。要实现浏览器宽度缩放时li整体居中。

### 实现方案一（未成功） ###
当我最初碰到这个问题的时候，第一反应是使用“margin： 0 auto;”来实现水平居中。只要能让ul包裹li，即ul的宽度为一行上所有li的宽度和，并让ul margin：0 auto;即可实现居中。
```
ul{
	margin: 0 auto;
	overflow: hidden;
}
li{
	width: 200px;
	float: left;	
}
```
[具体代码](http://codepen.io/zhangjialiang/pen/LVNaNE)
出现上述原因的主要问题是当子元素为浮动元素时（无论float还是inline），父元素是无所计算每行子元素宽度的，因此他在宽度上无法包裹子元素。
解决思路一：媒体查询，针对每行包含1,2...n个li时设置ul的宽度。

### 实现方案二 ###
第二种实现方案主要通过：text-align:center; 实现。根据inline和inline-block的元素均会在text-align：center的容器下居中，我们可以据此实现浮动居中。
```
ul{
	text-align: center;
}
li{
	display: inline-block;
}
```
[具体代码](http://codepen.io/zhangjialiang/pen/zGqXzN)
但是改解决方法存在一个非常明显且严重的问题，其最后一行的li个数如果有缺失的话，这不满一行的几个li也会居中，就会与上方样式不同。
解决方案一：在li最后加上多个无内容无高度的li，但是增加了很多无意义的标签，而且破环了html结构，也不算是纯css实现。

### 实现方案三 ###
通过position:relative;


