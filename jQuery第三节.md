
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