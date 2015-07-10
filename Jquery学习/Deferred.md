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
在promise中定义了 state, always, pipe, then, promise 5种方法。对于新建的Deferred对象，会使用jQuery.extend将promise方法绑定到对象上。
在这5个方法之后，有一个jQuery.each方法遍历了tuples，他通过表驱动将事件订阅和发布两组方法绑定到promise中。下面是遍历的源码，给出了部分注释。
```
jQuery.each( tuples, function( i, tuple ) {
			var list = tuple[ 2 ],
				stateString = tuple[ 5 ];

			// promise.progress = list.add
			// promise.done = list.add
			// promise.fail = list.add
			//在这里让事件订阅的三个方法成为回调队列的add方法，订阅事件的回调方法会放入Callbacks中
			promise[ tuple[1] ] = list.add;

			// Handle state
			//对于触发后有目标态的订阅事件，在其Callbacks中加入disable（阻止add和fire）或lock（阻止add）方法
			if ( stateString ) {
				list.add(
					function() {
						// state = "resolved" (i.e., fulfilled)
						// state = "rejected"
						state = stateString;
					},

					// rejected_callbacks.disable
					// fulfilled_callbacks.disable
					tuples[ 3 - i ][ 2 ].disable,

					// progress_callbacks.lock
					tuples[ 0 ][ 2 ].lock
				);
			}

			// progress_handlers.fire
			// fulfilled_handlers.fire
			// rejected_handlers.fire
			//事件发布其实就是触发Callbacks
			list.add( tuple[ 3 ].fire );

			// deferred.notify = function() { deferred.notifyWith(...) }
			// deferred.resolve = function() { deferred.resolveWith(...) }
			// deferred.reject = function() { deferred.rejectWith(...) }
			deferred[ tuple[0] ] = function() {
				deferred[ tuple[0] + "With" ]( this === deferred ? promise : this, arguments );
				return this;
			};

			// deferred.notifyWith = list.fireWith
			// deferred.resolveWith = list.fireWith
			// deferred.rejectWith = list.fireWith
			deferred[ tuple[0] + "With" ] = list.fireWith;
		});
```
tips：对于一些简单的数值操作，如果没有较大的复杂度区别，尽可能使用易读的操作。在遍历代码中tuples[ 3 - i ][ 2 ].disable,在较早的版本中使用的是tuples[ i ^ 1 ][ 2 ].disable。

下面看then的实现，老版本是pipe。
###then###
then中主要做的事包括以下几件：
* 返回一个新的promise对象
* 对返回的对象做一次过滤。
