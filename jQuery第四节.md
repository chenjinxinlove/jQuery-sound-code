
```
//检测支持
jQuery.support = (function( support ) {
    var input = document.createElement("input"),
		fragment = document.createDocumentFragment(),
		div = document.createElement("div"),
		select = document.createElement("select"),
		opt = select.appendChild( document.createElement("option") );
    //第一个 checkOn
    input.type = "checkbox";

	// Support: Safari 5.1, iOS 5.1, Android 4.x, Android 2.3
	// Check the default checkbox/radio value ("" on old WebKit; "on" elsewhere)
	support.checkOn = input.value !== "";
    
    //第二个 select的第一个子项是否是默认选中
    // Must access the parent to make an option select properly
	// Support: IE9, IE10
	support.optSelected = opt.selected;
    
    //定义初始值，得在dom加载完
	support.reliableMarginRight = true;
	support.boxSizingReliable = true;
	support.pixelPosition = false;
    
    // 选中checked 的状态 ，卡隆之后也应该被选中
	// Support: IE9, IE10
	input.checked = true;
	support.noCloneChecked = input.cloneNode( true ).checked;
	
	// selects被禁止，其子项也不应该被禁止
	// (WebKit marks them as disabled)
	select.disabled = true;
	support.optDisabled = !opt.disabled;
	
	// Check input变成radio后value是否也跟着变化
	// Support: IE9, IE10
	input = document.createElement("input");
	input.value = "t";
	input.type = "radio";
	support.radioValue = input.value === "t";
	
	input.setAttribute( "checked", "t" );
	input.setAttribute( "name", "t" );

	fragment.appendChild( input );
	
	// Support: Safari 5.1, Android 4.x, Android 2.3
	// old WebKit doesn't clone checked state correctly in fragments
	support.checkClone = fragment.cloneNode( true ).cloneNode( true ).lastChild.checked;

	// Support: Firefox, Chrome, Safari
	// onfocusin检测是否支持，有冒泡机制
	support.focusinBubbles = "onfocusin" in window;

    //背景剪切
	div.style.backgroundClip = "content-box";
	div.cloneNode( true ).style.backgroundClip = "";
	support.clearCloneStyle = div.style.backgroundClip === "content-box";
    
    //创建dom节点
	jQuery(function() {
		var container, marginDiv,
			// Support: Firefox, Android 2.3 (Prefixed box-sizing versions).
			//怪异模式  width：100px 在添加pading border会添加到里面 宽度就是100px，标准模式width+pading+border
			divReset = "padding:0;margin:0;border:0;display:block;-webkit-box-sizing:content-box;-moz-box-sizing:content-box;box-sizing:content-box",
			body = document.getElementsByTagName("body")[ 0 ];

		if ( !body ) {
			return;
		}

		container = document.createElement("div");
		container.style.cssText = "border:0;width:0;height:0;position:absolute;top:0;left:-9999px;margin-top:1px";

		// Check box-sizing and margin behavior.
		body.appendChild( container ).appendChild( div );
		div.innerHTML = "";
		// Support: Firefox, Android 2.3 (Prefixed box-sizing versions).
		//设置怪异模式
		div.style.cssText = "-webkit-box-sizing:border-box;-moz-box-sizing:border-box;box-sizing:border-box;padding:1px;border:1px;display:block;width:4px;margin-top:1%;position:absolute;top:1%";

		// Workaround failing boxSizing test due to offsetWidth returning wrong value
		// 检测是否支持怪异模式 box-sizing
		jQuery.swap( body, body.style.zoom != null ? { zoom: 1 } : {}, function() {
			support.boxSizing = div.offsetWidth === 4;
		});

		// nodejs就不会走哪里.
		if ( window.getComputedStyle ) {
			support.pixelPosition = ( window.getComputedStyle( div, null ) || {} ).top !== "1%";
			support.boxSizingReliable = ( window.getComputedStyle( div, null ) || { width: "4px" } ).width === "4px";

			// Support: Android 2.3
			// Check if div with explicit width and no margin-right incorrectly
			// gets computed margin-right based on width of container. (#3333)
			// WebKit Bug 13343 - getComputedStyle returns wrong value for margin-right
			marginDiv = div.appendChild( document.createElement("div") );
			marginDiv.style.cssText = div.style.cssText = divReset;
			marginDiv.style.marginRight = marginDiv.style.width = "0";
			div.style.width = "1px";

			support.reliableMarginRight =
				!parseFloat( ( window.getComputedStyle( marginDiv, null ) || {} ).marginRight );
		}

		body.removeChild( container );
	});
    return suport;
})({})

功能检测的方法结束
```

```
<!--数据缓存data()开始-->

<!--DOM元素和对象之间互相引用，大部分浏览器就会出现内存泄漏-->

<!--var oDiv = docment.getElement('div');-->
<!--var obj = {};-->

<!--oDiv.name = obj;-->
<!--obj.age = oDiv;-->


<!--简化版本------------------------>
jQuery.extend({
    acceptData
    hasData
    data
    removeData
    _data
    _removeData

})

jQuery.fn.extend({
    data
    removeData
})

Data.prototype = {
    key  建立映射防止内存泄漏
    set
    get
    access  //=>set(两个参数)/get(三个参数) => data
    remove
    hasData
    discard
}
<!--简化版本------------------------>
<!--使用 --------------------------->
$.data(docment.body,'age',30);
alert($.hasData(document.body,'age'));
$.removeData(document.body,'age');

<!--使用 --------------------------->
//定义几个标量
var data_user, data_priv,
	rbrace = /(?:\{[\s\S]*\}|\[[\s\S]*\])$/,
	rmultiDash = /([A-Z])/g;

function Data() {
	Object.defineProperty( this.cache = {}, 0, {
		get: function() {
			return {};
		}
	});
	//唯一标示
	this.expando = jQuery.expando + Math.random();
}

Data.uid = 1;

Data.accepts = function( owner ) {
//文本节点等就返回假
	return owner.nodeType ?
		owner.nodeType === 1 || owner.nodeType === 9 : true;
};

Data.prototype = {
	key: function( owner ) {
	//不是文本节点等
		if ( !Data.accepts( owner ) ) {
			return 0;
		}

		var descriptor = {},
			// 把唯一变量存起来
			unlock = owner[ this.expando ];

		// If not, create one
		if ( !unlock ) {
			unlock = Data.uid++;

			// Secure it in a non-enumerable, non-writable property
			try {
				descriptor[ this.expando ] = { value: unlock };
				Object.defineProperties( owner, descriptor );

			// Support: Android < 4
			// Fallback to a less secure definition
			} catch ( e ) {
				descriptor[ this.expando ] = unlock;
				jQuery.extend( owner, descriptor );
			}
		}

		// Ensure the cache object
		if ( !this.cache[ unlock ] ) {
			this.cache[ unlock ] = {};
		}

		return unlock;
	},
	set: function( owner, data, value ) {
		var prop,
			unlock = this.key( owner ),
			cache = this.cache[ unlock ];
		// Handle: [ owner, key, value ] args
		if ( typeof data === "string" ) {
			cache[ data ] = value;
		// Handle: [ owner, { properties } ] args
		} else {
			if ( jQuery.isEmptyObject( cache ) ) {
				jQuery.extend( this.cache[ unlock ], data );

			} else {
				for ( prop in data ) {
					cache[ prop ] = data[ prop ];
				}
			}
		}
		return cache;
	},
	get: function( owner, key ) {
		var cache = this.cache[ this.key( owner ) ];
		return key === undefined ?
			cache : cache[ key ];
	},
	access: function( owner, key, value ) {
		var stored;
		if ( key === undefined ||
				((key && typeof key === "string") && value === undefined) ) {
			stored = this.get( owner, key );
			return stored !== undefined ?
				stored : this.get( owner, jQuery.camelCase(key) );
		}
		this.set( owner, key, value );
		return value !== undefined ? value : key;
	},
	remove: function( owner, key ) {
		var i, name, camel,
			unlock = this.key( owner ),
			cache = this.cache[ unlock ];

		if ( key === undefined ) {
			this.cache[ unlock ] = {};

		} else {
			if ( jQuery.isArray( key ) ) {
				name = key.concat( key.map( jQuery.camelCase ) );
			} else {
				camel = jQuery.camelCase( key );
				// Try the string as a key before any manipulation
				if ( key in cache ) {
					name = [ key, camel ];
				} else {
					// If a key with the spaces exists, use it.
					// Otherwise, create an array by matching non-whitespace
					name = camel;
					name = name in cache ?
						[ name ] : ( name.match( core_rnotwhite ) || [] );
				}
			}

			i = name.length;
			while ( i-- ) {
				delete cache[ name[ i ] ];
			}
		}
	},
	hasData: function( owner ) {
		return !jQuery.isEmptyObject(
			this.cache[ owner[ this.expando ] ] || {}
		);
	},
	discard: function( owner ) {
		if ( owner[ this.expando ] ) {
			delete this.cache[ owner[ this.expando ] ];
		}
	}
};

// These may be used throughout the jQuery core codebase
data_user = new Data();
data_priv = new Data();


jQuery.extend({
	acceptData: Data.accepts,

	hasData: function( elem ) {
		return data_user.hasData( elem ) || data_priv.hasData( elem );
	},

	data: function( elem, name, data ) {
		return data_user.access( elem, name, data );
	},

	removeData: function( elem, name ) {
		data_user.remove( elem, name );
	},

	_data: function( elem, name, data ) {
		return data_priv.access( elem, name, data );
	},

	_removeData: function( elem, name ) {
		data_priv.remove( elem, name );
	}
});

jQuery.fn.extend({
	data: function( key, value ) {
		var attrs, name,
			elem = this[ 0 ],
			i = 0,
			data = null;

		// Gets all values
		if ( key === undefined ) {
			if ( this.length ) {
				data = data_user.get( elem );
                //data-ss 也都缓存
				if ( elem.nodeType === 1 && !data_priv.get( elem, "hasDataAttrs" ) ) {
				//得到全部属性
					attrs = elem.attributes;
					for ( ; i < attrs.length; i++ ) {
						name = attrs[ i ].name;
						if ( name.indexOf( "data-" ) === 0 ) {
							name = jQuery.camelCase( name.slice(5) );
							dataAttr( elem, name, data[ name ] );
						}
					}
					data_priv.set( elem, "hasDataAttrs", true );
				}
			}

			return data;
		}

		// Sets multiple values
		if ( typeof key === "object" ) {
			return this.each(function() {
				data_user.set( this, key );
			});
		}

		return jQuery.access( this, function( value ) {
			var data,
				camelKey = jQuery.camelCase( key );
			if ( elem && value === undefined ) {
			//最原始
				data = data_user.get( elem, key );
				if ( data !== undefined ) {
					return data;
				}
				//有转完 驼峰的
				data = data_user.get( elem, camelKey );
				if ( data !== undefined ) {
					return data;
				}
				//html5的值
				data = dataAttr( elem, camelKey, undefined );
				if ( data !== undefined ) {
					return data;
				}
				return;
			}

			// Set the data...
			this.each(function() {
				var data = data_user.get( this, camelKey );
				data_user.set( this, camelKey, value );
				if ( key.indexOf("-") !== -1 && data !== undefined ) {
					data_user.set( this, key, value );
				}
			});
		}, null, value, arguments.length > 1, null, true );
	},

	removeData: function( key ) {
		return this.each(function() {
			data_user.remove( this, key );
		});
	}
});
function dataAttr( elem, key, data ) {
	var name;

	// If nothing was found internally, try to fetch any
	// data from the HTML5 data-* attribute
	if ( data === undefined && elem.nodeType === 1 ) {
		name = "data-" + key.replace( rmultiDash, "-$1" ).toLowerCase();
		data = elem.getAttribute( name );

		if ( typeof data === "string" ) {
			try {
				data = data === "true" ? true :
					data === "false" ? false :
					data === "null" ? null :
					// Only convert to a number if it doesn't change the string
					+data + "" === data ? +data :
					rbrace.test( data ) ? JSON.parse( data ) :
					data;
			} catch( e ) {}

			// Make sure we set the data so it isn't changed later
			data_user.set( elem, key, data );
		} else {
			data = undefined;
		}
	}
	return data;
}
data()数据缓存完成
```


```
queue队列顺序的管理，先进先出 ，存的都是函数，在动画等有应用
   <!--$(function () {-->
   <!--    function a() {-->
   <!--    }-->
   <!--    function b() {-->
   <!--    }-->
   <!--    $.queue(document, 'q1',a);-->
   <!--    $.queue(document, 'q1',b);-->
   <!--    $.queue(document, 'q1',[a,b]);-->
   <!--$.dequeue(document,'q1') //a() 并自动执行-->
   <!--})-->

jQuery.extend({
	queue   //push()入队列
	dequeue  //shift() 出对列
	_queueHooks
	})
jQuery.fn.extend({
	queue
	dequeue
	delay
	clearQueue
	promise
	})
jQuery.extend({
//type是名字
    queue: function( elem, type, data ) {
    		var queue;
    
    		if ( elem ) {
    			type = ( type || "fx" ) + "queue";
    			queue = data_priv.get( elem, type );
    
    			// Speed up dequeue by getting out quickly if this is just a lookup
    			if ( data ) {
    			//如果queue不存在或者data是数组，就新建一个
    				if ( !queue || jQuery.isArray( data ) ) {
    					queue = data_priv.access( elem, type, jQuery.makeArray(data) );
    				} else {
    					queue.push( data );
    				}
    			}
    			return queue || [];
    		}
    	},
    	dequeue: function( elem, type ) {
		type = type || "fx";

		var queue = jQuery.queue( elem, type ),
			startLength = queue.length,
			fn = queue.shift(),
			//去除缓存
			hooks = jQuery._queueHooks( elem, type ),
			next = function() {
				jQuery.dequeue( elem, type );
			};
        //inprogress"是默认的动画操作
		// If the fx queue is dequeued, always remove the progress sentinel
		if ( fn === "inprogress" ) {
			fn = queue.shift();
			startLength--;
		}

		if ( fn ) {

			// Add a progress sentinel to prevent the fx queue from being
			// automatically dequeued
			if ( type === "fx" ) {
				queue.unshift( "inprogress" );
			}

			// clear up the last queue stop function
			delete hooks.stop;
			//执行
			fn.call( elem, next, hooks );
		}

		if ( !startLength && hooks ) {
			hooks.empty.fire();
		}
	},
	_queueHooks: function( elem, type ) {
		var key = type + "queueHooks";
		return data_priv.get( elem, key ) || data_priv.access( elem, key, {
			empty: jQuery.Callbacks("once memory").add(function() {
				data_priv.remove( elem, [ type + "queue", key ] );
			})
		});
	}
})
//$(document).queue('q1',aaa)
jQuery.fn.extend({
	queue: function( type, data ) {
		var setter = 2;

		if ( typeof type !== "string" ) {
			data = type;
			type = "fx";
			setter--;
		}

		if ( arguments.length < setter ) {
			return jQuery.queue( this[0], type );
		}

		return data === undefined ?
			this :
			this.each(function() {
				var queue = jQuery.queue( this, type, data );

				// ensure a hooks for this queue
				jQuery._queueHooks( this, type );
                //动画自动出队
				if ( type === "fx" && queue[0] !== "inprogress" ) {
					jQuery.dequeue( this, type );
				}
			});
	},
	dequeue: function( type ) {
		return this.each(function() {
			jQuery.dequeue( this, type );
		});
	},
	// Based off of the plugin by Clint Helfers, with permission.
	// http://blindsignals.com/index.php/2009/07/jquery-delay/
	delay: function( time, type ) {
<!--	//jQuery.fx.speeds = {-->
<!--	slow: 600,-->
<!--	fast: 200,-->
<!--	// Default speed-->
<!--	_default: 400-->
<!--};-->支持字符串
		time = jQuery.fx ? jQuery.fx.speeds[ time ] || time : time;
		type = type || "fx";

		return this.queue( type, function( next, hooks ) {
			var timeout = setTimeout( next, time );
			hooks.stop = function() {
				clearTimeout( timeout );
			};
		});
	},
	clearQueue: function( type ) {
		return this.queue( type || "fx", [] );
	},
	// Get a promise resolved when queues of a certain type
	// 等待队列都结束，在开始出发$(index).promise().done(function(){})
	promise: function( type, obj ) {
		var tmp,
			count = 1,
			defer = jQuery.Deferred(),
			elements = this,
			i = this.length,
			resolve = function() {
				if ( !( --count ) ) {
					defer.resolveWith( elements, [ elements ] );
				}
			};

		if ( typeof type !== "string" ) {
			obj = type;
			type = undefined;
		}
		type = type || "fx";

		while( i-- ) {
			tmp = data_priv.get( elements[ i ], type + "queueHooks" );
			if ( tmp && tmp.empty ) {
				count++;
				tmp.empty.add( resolve );
			}
		}
		resolve();
		return defer.promise( obj );
	}
});
```