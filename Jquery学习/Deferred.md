#Deferred#
Deferred(递延)，是Jquery中满足promise规范的一种延时（异步）调用方式，能够极大改善回调函数带来的代码结构的问题，并且清晰调用步骤。
```
jQuery.extend({

	Deferred: function ( func ) {},

	when: function ( subordinate /* , ..., subordinateN */ ) {}
});
```
将Deferred.js中代码的框架如上所示，提供了Deferred和when两个静态函数，extend()是jQuery中扩展函数的一种方式。
####Deferred####
在Deferred中定义了 tuples，记录了Deferred的3种状态：
```
var tuples = [
				// action, add listener, callbacks,
				// ... .then handlers, argument index, [final state]
				[ "notify", "progress", jQuery.Callbacks("memory"),
					jQuery.Callbacks("memory"), 2 ],
				[ "resolve", "done", jQuery.Callbacks("once memory"),
					jQuery.Callbacks("once memory"), 0, "resolved" ],
				[ "reject", "fail", jQuery.Callbacks("once memory"),
					jQuery.Callbacks("once memory"), 1, "rejected" ]
			]
```
每种状态分别包含的参数是：动作，监听器，回调，处理，参数索引，最终状态。这里的动作和监听器构成了sub/pub模式，其中done,fail,progress是事件订阅；notify,resolve,reject是事件发布。
```
var d = $.Deferred();
d.fail(function () {
	console.log('fail');
});
d.reject();
> fail
```
其中jQuery.Callbacks是JQ的一个回调对象，可以用来添加回调，执行回调，删除回调等等。
在promise中定义了 state, always, pipe, then, promise 5种方法。