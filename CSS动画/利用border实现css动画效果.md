#利用border实现各种动画效果#
![](http://7xj8rs.com1.z0.glb.clouddn.com/Snip20150523_1.png?attname=&e=1432454444&token=AFJKN17FaMIRe1niIifOsewbLmQ7AfOWTFtM7BgZ:25PfwmALCEFvrJYzxevdQQ5-NAo)
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