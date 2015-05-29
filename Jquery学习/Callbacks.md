#Callbacks#
Callbacks是jQuery内部提供的供ajax，deferred等使用的回调队列的封装。提供了以下方法供使用:
```
callbacks.add()        回调列表中添加一个回调或回调的集合。
callbacks.disable()    禁用回调列表中的回调
callbacks.disabled()   确定回调列表是否已被禁用。 
callbacks.empty()      从列表中删除所有的回调.
callbacks.fire()       用给定的参数调用所有的回调
callbacks.fired()      访问给定的上下文和参数列表中的所有回调。 
callbacks.fireWith()   访问给定的上下文和参数列表中的所有回调。
callbacks.has()        确定列表中是否提供一个回调
callbacks.lock()       锁定当前状态的回调列表。
callbacks.locked()     确定回调列表是否已被锁定。
callbacks.remove()     从回调列表中的删除一个回调或回调集合。
```
其中最为关键的是add和fire两个方法，直接看代码：
```
add: function() {
				if ( list ) {

					// If we have memory from a past run, we should fire after adding
					if ( memory && !firing ) {
						firingIndex = list.length 1;
						queue.push( memory );
					}

					(function add( args ) {
						jQuery.each( args, function( _, arg ) {
							if ( jQuery.isFunction( arg ) ) {
								if ( !options.unique || !self.has( arg ) ) {
									list.push( arg );
								}
							} else if ( arg && arg.length && jQuery.type( arg ) !== "string" ) {
								// Inspect recursively
								add( arg );
							}
						});
					})( arguments );

					if ( memory && !firing ) {
						fire();
					}
				}
				return this;
}
```
* if(list)在Callbacks被lock的时候会返回false，这时候无法调用add方法
* if ( memory && !firing )意味着该回调正在执行中，所以当add调用结束后仍要调用fire方法
* 接下来通过一个立即调用函数往队列里增加回调函数，对于传入参数，如果是Function，则直接push，如果是list，则递归调用add方法。所以$.Callbacks.add(),可以传入的参数包括（Function）,(Function1,Function2),([Function1,Function2])以上三种。
在看一下fire方法，在self里封装的fire方法会调用fireWith，freWith处理过传入参数后判断当前是否处于firing状态，如果不的话调用私有的fire方法。典型的装饰者模式。
```
// Call all callbacks with the given context and arguments
			fireWith: function( context, args ) {
				if ( !locked ) {
					args = args || [];
					args = [ context, args.slice ? args.slice() : args ];
					queue.push( args );
					if ( !firing ) {
						fire();
					}
				}
				return this;
			},

			// Call all the callbacks with the given arguments
			fire: function() {
				self.fireWith( this, arguments );
				return this;
			},
```
接下来看下fire方法，截取了源代码，删除了原有注释：
```
fire = function() {

			//在这里判断是否含有参数once，如果有的话该callbacks只会被调用一次
			locked = options.once;
			
			//触发回调且正在回调中
			fired = firing = true;
			//queue 中存放了触发fire传入的参数
			for ( ; queue.length; firingIndex = -1 ) {
				memory = queue.shift();
				while ( ++firingIndex < list.length ) {

					// 取出list中的回调函数，传入参数执行，memory[0]和[1]分别保上下文和fire参数
					if ( list[ firingIndex ].apply( memory[ 0 ], memory[ 1 ] ) === false &&
						options.stopOnFalse ) {

						//执行失败是stop的话中止，且清空memory防止add后立即调用
						firingIndex = list.length;
						memory = false;
					}
				}
			}

			// 执行一次参数清空
			if ( !options.memory ) {
				memory = false;
			}

			firing = false;

			// Clean up if we're done firing for good
			if ( locked ) {

				// Keep an empty list if we have data for future add calls
				if ( memory ) {
					list = [];

				// Otherwise, this object is spent
				} else {
					list = "";
				}
			}
		}
```