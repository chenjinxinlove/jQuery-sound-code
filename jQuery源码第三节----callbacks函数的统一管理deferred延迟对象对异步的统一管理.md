
```
<!--var optionsCache = {};-->
<!--function createOptions( options ) {-->
<!--	var object = optionsCache[ options ] = {};-->
<!--	jQuery.each( options.match( core_rnotwhite ) || [], function( _, flag ) {-->
<!--		object[ flag ] = true;-->
<!--	});-->
<!--	return object;-->
<!--}-->


jQuery.Callbacks = function(options) {
//"once memory" = > {once : true ,memory :true}
    options = typeof options === "string" ?
    //缓存中有，就不去找了
    (optionsCache[options] || createOptions(options)) :
    //不写
    jQuery.extend({},options);
    
    var
		memory,
	
		fired,
		
		firing,
		
		firingStart,

		firingLength,
	
		firingIndex,
		// 存储组数的列表
		list = [],
		//不存在就是false
		stack = !options.once && [],
	
		fire = function( data ) {
			memory = options.memory && data;
			//调用过一次
			fired = true;
			firingIndex = firingStart || 0;
			firingStart = 0;
			firingLength = list.length;
			firing = true;
			for ( ; list && firingIndex < firingLength; firingIndex++ ) {
				if ( list[ firingIndex ].apply( data[ 0 ], data[ 1 ] ) === false && options.stopOnFalse ) {
					memory = false; 
					break;
				}
			}
			firing = false;
			if ( list ) {
				if ( stack ) {
					if ( stack.length ) {
						fire( stack.shift() );
					}
				} else if ( memory ) {
					list = [];
				} else {
					self.disable();
				}
			}
		},
		self = {
			// Add a callback or a collection of callbacks to the list
			add: function() {
				if ( list ) {
					// First, we save the current length
					var start = list.length;
					//add(a,b)那种情况
					(function add( args ) {
						jQuery.each( args, function( _, arg ) {
							var type = jQuery.type( arg );
							//单个的函数
							if ( type === "function" ) {
							//唯一性，有就不添加
								if ( !options.unique || !self.has( arg ) ) {
									list.push( arg );
								}
								//如果是[aa,bb]数组的形式
							} else if ( arg && arg.length && type !== "string" ) {
			                    //递归调用
								add( arg );
							}
						});
					})( arguments );
					// Do we need to add the callbacks to the
					// current firing batch?
					if ( firing ) {
						firingLength = list.length;
					// With memory, if we're not firing then
					// we should call right away
					} else if ( memory ) {
						firingStart = start;
						fire( memory );
					}
				}
				return this;
			},
			// 移除 在数组中
			remove: function() {
				if ( list ) {
					jQuery.each( arguments, function( _, arg ) {
						var index;
						while( ( index = jQuery.inArray( arg, list, index ) ) > -1 ) {
							list.splice( index, 1 );
							// Handle firing indexes
							if ( firing ) {
								if ( index <= firingLength ) {
									firingLength--;
								}
								if ( index <= firingIndex ) {
									firingIndex--;
								}
							}
						}
					});
				}
				return this;
			},
		    //是否在list中
			has: function( fn ) {
				return fn ? jQuery.inArray( fn, list ) > -1 : !!( list && list.length );
			},
			// list全部取出，空
			empty: function() {
				list = [];
				firingLength = 0;
				return this;
			},
			// 禁止所以
			disable: function() {
				list = stack = memory = undefined;
				return this;
			},
			// 判断是否禁止
			disabled: function() {
				return !list;
			},
			// Lock the list in its current state
			lock: function() {
				stack = undefined;
				if ( !memory ) {
					self.disable();
				}
				return this;
			},
			// Is it locked?
			locked: function() {
				return !stack;
			},
			// Call all callbacks with the given context and arguments
			fireWith: function( context, args ) {
			//第一次执行fired就是true 取反 stack 由上面once决定
				if ( list && ( !fired || stack ) ) {
					args = args || [];
					args = [ context, args.slice ? args.slice() : args ];
					if ( firing ) {
						stack.push( args );
					} else {
						fire( args );
					}
				}
				return this;
			},
			// Call all the callbacks with the given arguments
			fire: function() {
				self.fireWith( this, arguments );
				return this;
			},
			// To know if the callbacks have already been called at least once
			fired: function() {
				return !!fired;
			}
		};

	return self;
};
}



```
~~++~~回调对象~~ : 函数的统一管理++~~
```

```

```
//defer [dɪ'fɜː] 推迟；延期；服从
jQuery.extend({
    
    Deferred : function(){},
    //多个延迟
    when : function(){}


})

jQuery.extend({

	Deferred: function( func ) {
<!--显而易见Deferred是个工厂类，返回的是内部构建的deferred对象-->
<!--tuples 创建三个$.Callbacks对象，分别表示成功，失败，处理中三种状态-->
<!--创建了一个promise对象，具有state、always、then、primise方法-->
<!--扩展primise对象生成最终的Deferred对象，返回该对象-->
		var tuples = [
				// action, add listener, listener list, final state
				[ "resolve", "done", jQuery.Callbacks("once memory"), "resolved" ],
				[ "reject", "fail", jQuery.Callbacks("once memory"), "rejected" ],
				[ "notify", "progress", jQuery.Callbacks("memory") ]
			],
			state = "pending",
			promise = {
				state: function() {
					return state;
				},
				always: function() {
					deferred.done( arguments ).fail( arguments );
					return this;
				},
				then: function( /* fnDone, fnFail, fnProgress */ ) {
					var fns = arguments;
					return jQuery.Deferred(function( newDefer ) {
						jQuery.each( tuples, function( i, tuple ) {
							var action = tuple[ 0 ],
								fn = jQuery.isFunction( fns[ i ] ) && fns[ i ];
							// deferred[ done | fail | progress ] for forwarding actions to newDefer
							deferred[ tuple[1] ](function() {
								var returned = fn && fn.apply( this, arguments );
								if ( returned && jQuery.isFunction( returned.promise ) ) {
									returned.promise()
										.done( newDefer.resolve )
										.fail( newDefer.reject )
										.progress( newDefer.notify );
								} else {
									newDefer[ action + "With" ]( this === promise ? newDefer.promise() : this, fn ? [ returned ] : arguments );
								}
							});
						});
						fns = null;
					}).promise();
				},
				// 最后合并promise到deferred
				promise: function( obj ) {
					return obj != null ? jQuery.extend( obj, promise ) : promise;
				}
			},
			deferred = {};

		// Keep pipe for back-compat
		promise.pipe = promise.then;

		// Add list-specific methods
		jQuery.each( tuples, function( i, tuple ) {
			var list = tuple[ 2 ],
				stateString = tuple[ 3 ];
            //第一部分将回调函数存入
            <!--promise.done = $.Callbacks("once memory").add-->
            <!--promise.fail = $.Callbacks("once memory").add-->
            <!--promise.progressl = $.Callbacks("memory").add-->
			// promise[ done | fail | progress ] = list.add
			promise[ tuple[1] ] = list.add;

			<!--如果存在deferred最终状态-->
            <!--默认会预先向doneList,failList中的list添加三个回调函数-->
			if ( stateString ) {
				list.add(function() {
					// state = [ resolved | rejected ]
					state = stateString;

				// [ reject_list | resolve_list ].disable; progress_list.lock
				//第二个传参数是1、0索引对调了，所以取值是failList.disable与doneList.disable
				}, tuples[ i ^ 1 ][ 2 ].disable, tuples[ 2 ][ 2 ].lock );
			}

			// deferred[ resolve | reject | notify ]
			deferred[ tuple[0] ] = function() {
				deferred[ tuple[0] + "With" ]( this === deferred ? promise : this, arguments );
				return this;
			};
			deferred[ tuple[0] + "With" ] = list.fireWith;
		});

		// Make the deferred a promise
		promise.promise( deferred );

		// Call given func if any
		if ( func ) {
			func.call( deferred, deferred );
		}

		// All done!
		return deferred;
	},

	// Deferred helper
	when: function( subordinate /* , ..., subordinateN */ ) {
		var i = 0,
			resolveValues = core_slice.call( arguments ),
			length = resolveValues.length,

			// the count of uncompleted subordinates
			remaining = length !== 1 || ( subordinate && jQuery.isFunction( subordinate.promise ) ) ? length : 0,

			// the master Deferred. If resolveValues consist of only a single Deferred, just use that.
			deferred = remaining === 1 ? subordinate : jQuery.Deferred(),

			// Update function for both resolve and progress values
			updateFunc = function( i, contexts, values ) {
				return function( value ) {
					contexts[ i ] = this;
					values[ i ] = arguments.length > 1 ? core_slice.call( arguments ) : value;
					if( values === progressValues ) {
						deferred.notifyWith( contexts, values );
					} else if ( !( --remaining ) ) {
						deferred.resolveWith( contexts, values );
					}
				};
			},

			progressValues, progressContexts, resolveContexts;

		// add listeners to Deferred subordinates; treat others as resolved
		if ( length > 1 ) {
			progressValues = new Array( length );
			progressContexts = new Array( length );
			resolveContexts = new Array( length );
			for ( ; i < length; i++ ) {
				if ( resolveValues[ i ] && jQuery.isFunction( resolveValues[ i ].promise ) ) {
					resolveValues[ i ].promise()
						.done( updateFunc( i, resolveContexts, resolveValues ) )
						.fail( deferred.reject )
						.progress( updateFunc( i, progressContexts, progressValues ) );
				} else {
					--remaining;
				}
			}
		}

		// if we're not waiting on anything, resolve the master
		if ( !remaining ) {
			deferred.resolveWith( resolveContexts, resolveValues );
		}

		return deferred.promise();
	}
});

```
这样看来，Promise/A只是一种规范，Deferred可以看作这种规范的具体实现
![image](http://images.cnitblog.com/blog/329084/201310/02174356-497be81e7a2749ab94609f4d026b8d0f.jpg)
promise回调会在处于以下两种不同的状态下执行：

resolved：在这种情况下，数据是可用
rejected：在这种情况下，出现了错误，没有可用的值
'then'方法接受两个参数：一个用于promise得到了解决（resolved），另一个用于promise拒绝（rejected）。让我们回到伪代码

promise.then( function( futureValue ) {   
   //成功    
} , function() {   
  //失败
} );
在某些情况下，我们需要获得多个返回结果后，再继续执行应用程序（例如，在用户可以选择他们感兴趣的选项前，显示一组动态的选项）。这种情况下，'when'方法可以用来解决所有的promise都满足后才能继续执行的场景。

when(   
  promise1,   
  promise2,  
   ...  
 ).then(function( futureValue1, futureValue2, ... ) {   
     //当所有的异步对象都准备完毕后，才执行
 });
 
 

api | 作用
---|---
jQuery.Deferred() |一个构造函数，返回一个链式实用对象方法来注册多个回调，回调队列， 调用回调队列，并转达任何同步或异步函数的成功或失败状态。
deferred.always()|当Deferred（延迟）对象解决或拒绝时，调用添加处理程序
deferred.done()|当Deferred（延迟）对象解决时，调用添加处理程序
deferred.fail()|当Deferred（延迟）对象拒绝时，调用添加处理程序。
deferred.isRejected()|确定一个Deferred（延迟）对象是否已被拒绝。
deferred.isResolved()|确定一个Deferred（延迟）对象是否已被解决。
deferred.notify()|根据给定的 args参数 调用Deferred（延迟）对象上进行中的回调 （progressCallbacks）
deferred.notifyWith()|根据给定的上下文（context）和args递延调用Deferred（延迟）对象上进行中的回调（progressCallbacks ）
deferred.pipe()|实用的方法来过滤 and/or 链Deferreds。
deferred.progress()|当Deferred（延迟）对象生成进度通知时，调用添加处理程
deferred.promise()|返回Deferred(延迟)的Promise（承诺）对象。
deferred.reject()|拒绝Deferred（延迟）对象，并根据给定的args参数调用任何失败回调函数（failCallbacks）
deferred.rejectWith()|拒绝Deferred（延迟）对象，并根据给定的 context和args参数调用任何失败回调函数（failCallbacks）。
deferred.resolve()|解决Deferred（延迟）对象，并根据给定的args参数调用任何完成回调函数（doneCallbacks）
deferred.resolveWith()|解决Deferred（延迟）对象，并根据给定的 context和args参数调用任何完成回调函数（doneCallbacks）
deferred.state()|确定一个Deferred（延迟）对象的当前状态
deferred.then()|当Deferred（延迟）对象解决，拒绝或仍在进行中时，调用添加处理程序。
jQuery.when()|提供一种方法来执行一个或多个对象的回调函数， Deferred(延迟)对象通常表示异步事件。
.promise()|返回一个 Promise 对象用来观察当某种类型的所有行动绑定到集合，排队与否还是已经完成


```
var defer = $.Deferred(); //构建异步对象

  setTimeout(function(){
    defer.resolve( 5 );    
  },1000);

  var filtered  = defer.then(function( value ) {
        return value * 2;
  });

  filtered.done(function( value ) {
      console.log('打印出值',value)
  });
```

构建Deferred对象时候的流程图
![image](http://images.cnitblog.com/blog/329084/201310/07212436-77fb87abe03846cd857da70ab1698c32.jpg)


```
var defer = $.Deferred(); //构建异步对象
的时候,内部的对象就有了4个属性方法了

deferred: Object
always: function () {
done: function () {
fail: function () {
notify: function () {
notifyWith: function ( context, args ) {
pipe: function ( /* fnDone, fnFail, fnProgress */ ) {
progress: function () {
promise: function ( obj ) {
reject: function () {
rejectWith: function ( context, args ) {
resolve: function () {
resolveWith: function ( context, args ) {
state: function () {
then: function ( /* fnDone, fnFail, fnProgress */ ) {
promise: Object
always: function () {
done: function () {
fail: function () {
pipe: function ( /* fnDone, fnFail, fnProgress */ ) {
progress: function () {
promise: function ( obj ) {
state: function () {
then: function ( /* fnDone, fnFail, fnProgress */ ) {
state: "pending"
tuples: Array[3]
```



![image](http://images.cnitblog.com/blog/329084/201310/07212451-50defafec7bc468f817adb82476c9bb9.jpg)