#利用border实现各种动画效果#
![](http://7xj8rs.com1.z0.glb.clouddn.com/Snip20150523_1.png?attname=&e=1432454444&token=AFJKN17FaMIRe1niIifOsewbLmQ7AfOWTFtM7BgZ:25PfwmALCEFvrJYzxevdQQ5-NAo)
[点我看上图动画效果](http://codepen.io/zhangjialiang/pen/EjKrRO)
上图中给出的各种图形就是利用border实现的动画效果，它们的原理都是一致的，通过多层div相互包裹，每个div设置不同的border，通过css animation使得border旋转，从而实现各种动画效果。下面会以第一个动画为例进行详解。
####一. dom结构####
```
<div class="content animate-all">
  <div class="spinner"></div>
</div>
```
基础的dom结构如上所示，背景内包含一个div，div的层层嵌套通过js实现。
```
$('.spinner').each(function () {
  var html = '';
  for (var i = 0; i < 10; i++) {
    html = '<div>' + html + '</div>';
  }
  $(this).html(html);
});
```
###二. 样式CSS###
```
body {
  background: #333;
  overflow: auto;
}

.content {
  text-align: center;
}

.spinner {
  display: inline-block;
  margin: 30px;
}

.spinner div {
  display: inline-block;
  border-top: 5px solid #FF9000;
  border-left: 5px solid #A101A6;
  border-right: 5px solid #CFF700;
  border-bottom: 5px solid #0A64A4;
  border-radius: 50%;
  padding: 3px;
  line-height: 0;
}
```
需要注意的是spinner设为inline-block是为了利用text-align: center的效果实现居中，而.spinner div设为inline-block的目的是使元素具有包裹性。另外.spinner div的line-height必须设为0，消除inline-block默认normal带来的高度影响。
以上代码完成了我们动画之前的全部准备，可以得到以下图形：
![](http://7xj8rs.com1.z0.glb.clouddn.com/Snip20150523_2.png?imageView2/2/w/203/h/203&e=1432459118&token=AFJKN17FaMIRe1niIifOsewbLmQ7AfOWTFtM7BgZ:FPUKXGh3he9gNF29QpIC6xtO6Ag)
###三. 实现动画###
```
.animate-all .spinner div,
.spinner:hover div {
  animation: spin 5s linear infinite;
  -webkit-animation: spin 5s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@-webkit-keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
```
动画的代码非常的简单，如上所述，其实就是设置每个div在5s内线性绕一圈，由于div的旋转会影响其子元素，所以每个子元素会比其父元素快一圈（其自身的一圈）。
[详细demo代码](http://codepen.io/zhangjialiang/pen/gpwEGM)















