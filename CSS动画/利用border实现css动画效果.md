#利用border实现各种动画效果#
<style type="text/css">
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

.animate-all .spinner div,
.spinner:hover div {
  animation: spin 5s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.spinner.v2 div {
  border-top: 5px solid #00f;
  border-right: 4px solid #00f;
  border-bottom: 5px solid #00f;
  border-left: 4px solid #00f;
}

.spinner.v3 div {
  border-top-color: green;
  border-right: 0;
  border-bottom-color: black;
  border-left: 0;
}

.spinner.v4 div {
  border-top: 0;
  border-right-color: green;
  border-bottom: 0;
  border-left-color: black;
  animation-timing-function: ease-in-out;
}

.spinner.v5 div {
  border-top: 0;
  border-right: 0;
  border-bottom-color: white;
  border-left-color: white;
}

.spinner.v6 div {
  border-top: 0;
  border-right: 0;
  border-bottom-color: white;
  border-left-color: black;
}

.spinner.v7 div {
  border-style: dotted;
}

.spinner.v8 div {
  border-style: dashed;
}

.spinner.v9 div {
  border-top: 1px dashed black;
  border-right: 5px double black;
  border-bottom: 3px dotted black;
  border-left: 5px double black;
}

.spinner.v10 div {
  border-top: 0;
  border-right: 0;
  border-bottom: 4px double red;
  border-left: 8px solid white;
}

.spinner.v11 div {
  border-radius: 0 50% 0 50%;
}

.spinner.v12 div {
  border-radius: 0;
  border-top: 0;
  border-right-color: green;
  border-bottom: 0;
  border-left-color: black;
}

.spinner.v13 div {
  border-radius: 30px;
  border-top: 0;
  border-bottom: 0;
  border-left: 0;
}

.spinner.v14 div {
  border-radius: 50% 0 50% 0;
  border-right: 0;
  border-left: 0;
}

.spinner.v15 div {
  border-top: 0;
  border-right: 0;
  border-color: black;
  animation-name: v15;
}

@keyframes v15 {
  0% {
    transform: rotate(0deg);
  }
  50% {
    background: black;
  }
  100% {
    transform: rotate(360deg);
  }
}

.spinner.v16 div {
  border-top: 0;
  border-right: 0;
  border-color: black;
  animation-name: v16;
}

@keyframes v16 {
  0% {
    transform: rotate(0deg);
  }
  50% {
    border-color: #222;
    background: black;
  }
  100% {
    transform: rotate(360deg);
  }
}

.spinner.v17 div {
  border-style: double;
  border-color: black;
  border-top-width: 0;
  border-bottom-width: 0;
  animation-name: v17;
}

@keyframes v17 {
  0% {
    border-right-color: black;
    border-left-color: black;
    transform: rotate(0deg);
  }
  25% {
    border-right-color: black;
    border-left-color: green;
  }
  50% {
    border-right-color: green;
    border-left-color: green;
    background: black;
  }
  75% {
    border-right-color: black;
    border-left-color: green;
  }
  100% {
    border-right-color: black;
    border-left-color: black;
    transform: rotate(360deg);
  }
}
</style>
<script>
$('.spinner').each(function() {
  var $spinner = $(this),
      size = $spinner.data('size') || 10,
      html = '';
  
  for (var i = 0; i < size; ++i) {
    html = '<div>' + html + '</div>';
  }
  
  $spinner.html(html);
});
</script>
<div class="content animate-all"><div class="spinner"></div><div class="spinner v2" data-size="4"></div><div class="spinner v3"></div><div class="spinner v4"></div><div class="spinner v5"></div><div class="spinner v6"></div><div class="spinner v7"></div><div class="spinner v8"></div><div class="spinner v9"></div><div class="spinner v10"></div><div class="spinner v11"></div><div class="spinner v12"></div><div class="spinner v13"></div><div class="spinner v14"></div><div class="spinner v15"></div><div class="spinner v16"></div><div class="spinner v17" data-size="13"></div></div>
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















