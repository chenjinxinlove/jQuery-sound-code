
```
//���֧��
jQuery.support = (function( support ) {
    var input = document.createElement("input"),
		fragment = document.createDocumentFragment(),
		div = document.createElement("div"),
		select = document.createElement("select"),
		opt = select.appendChild( document.createElement("option") );
    //��һ�� checkOn
    input.type = "checkbox";

	// Support: Safari 5.1, iOS 5.1, Android 4.x, Android 2.3
	// Check the default checkbox/radio value ("" on old WebKit; "on" elsewhere)
	support.checkOn = input.value !== "";
    
    //�ڶ��� select�ĵ�һ�������Ƿ���Ĭ��ѡ��
    // Must access the parent to make an option select properly
	// Support: IE9, IE10
	support.optSelected = opt.selected;
    
    //�����ʼֵ������dom������
	support.reliableMarginRight = true;
	support.boxSizingReliable = true;
	support.pixelPosition = false;
    
    // ѡ��checked ��״̬ ����¡֮��ҲӦ�ñ�ѡ��
	// Support: IE9, IE10
	input.checked = true;
	support.noCloneChecked = input.cloneNode( true ).checked;
	
	// selects����ֹ��������Ҳ��Ӧ�ñ���ֹ
	// (WebKit marks them as disabled)
	select.disabled = true;
	support.optDisabled = !opt.disabled;
	
	// Check input���radio��value�Ƿ�Ҳ���ű仯
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
	// onfocusin����Ƿ�֧�֣���ð�ݻ���
	support.focusinBubbles = "onfocusin" in window;

    //��������
	div.style.backgroundClip = "content-box";
	div.cloneNode( true ).style.backgroundClip = "";
	support.clearCloneStyle = div.style.backgroundClip === "content-box";
    
    //����dom�ڵ�
	jQuery(function() {
		var container, marginDiv,
			// Support: Firefox, Android 2.3 (Prefixed box-sizing versions).
			//����ģʽ  width��100px �����pading border����ӵ����� ��Ⱦ���100px����׼ģʽwidth+pading+border
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
		//���ù���ģʽ
		div.style.cssText = "-webkit-box-sizing:border-box;-moz-box-sizing:border-box;box-sizing:border-box;padding:1px;border:1px;display:block;width:4px;margin-top:1%;position:absolute;top:1%";

		// Workaround failing boxSizing test due to offsetWidth returning wrong value
		// ����Ƿ�֧�ֹ���ģʽ box-sizing
		jQuery.swap( body, body.style.zoom != null ? { zoom: 1 } : {}, function() {
			support.boxSizing = div.offsetWidth === 4;
		});

		// nodejs�Ͳ���������.
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

���ܼ��ķ�������
```

```
<!--���ݻ���data()��ʼ-->

<!--DOMԪ�غͶ���֮�以�����ã��󲿷�������ͻ�����ڴ�й©-->

<!--var oDiv = docment.getElement('div');-->
<!--var obj = {};-->

<!--oDiv.name = obj;-->
<!--obj.age = oDiv;-->


<!--�򻯰汾------------------------>
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
    key  ����ӳ���ֹ�ڴ�й©
    set
    get
    access  //=>set(��������)/get(��������) => data
    remove
    hasData
    discard
}
<!--�򻯰汾------------------------>
<!--ʹ�� --------------------------->
$.data(docment.body,'age',30);
alert($.hasData(document.body,'age'));
$.removeData(document.body,'age');

<!--ʹ�� --------------------------->
//���弸������
var data_user, data_priv,
	rbrace = /(?:\{[\s\S]*\}|\[[\s\S]*\])$/,
	rmultiDash = /([A-Z])/g;

function Data() {
	Object.defineProperty( this.cache = {}, 0, {
		get: function() {
			return {};
		}
	});
	//Ψһ��ʾ
	this.expando = jQuery.expando + Math.random();
}

Data.uid = 1;

Data.accepts = function( owner ) {
//�ı��ڵ�Ⱦͷ��ؼ�
	return owner.nodeType ?
		owner.nodeType === 1 || owner.nodeType === 9 : true;
};

Data.prototype = {
	key: function( owner ) {
	//�����ı��ڵ��
		if ( !Data.accepts( owner ) ) {
			return 0;
		}

		var descriptor = {},
			// ��Ψһ����������
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
                //data-ss Ҳ������
				if ( elem.nodeType === 1 && !data_priv.get( elem, "hasDataAttrs" ) ) {
				//�õ�ȫ������
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
			//��ԭʼ
				data = data_user.get( elem, key );
				if ( data !== undefined ) {
					return data;
				}
				//��ת�� �շ��
				data = data_user.get( elem, camelKey );
				if ( data !== undefined ) {
					return data;
				}
				//html5��ֵ
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
data()���ݻ������
```


```
queue����˳��Ĺ����Ƚ��ȳ� ����Ķ��Ǻ������ڶ�������Ӧ��
   <!--$(function () {-->
   <!--    function a() {-->
   <!--    }-->
   <!--    function b() {-->
   <!--    }-->
   <!--    $.queue(document, 'q1',a);-->
   <!--    $.queue(document, 'q1',b);-->
   <!--    $.queue(document, 'q1',[a,b]);-->
   <!--$.dequeue(document,'q1') //a() ���Զ�ִ��-->
   <!--})-->

jQuery.extend({
	queue   //push()�����
	dequeue  //shift() ������
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
//type������
    queue: function( elem, type, data ) {
    		var queue;
    
    		if ( elem ) {
    			type = ( type || "fx" ) + "queue";
    			queue = data_priv.get( elem, type );
    
    			// Speed up dequeue by getting out quickly if this is just a lookup
    			if ( data ) {
    			//���queue�����ڻ���data�����飬���½�һ��
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
			//ȥ������
			hooks = jQuery._queueHooks( elem, type ),
			next = function() {
				jQuery.dequeue( elem, type );
			};
        //inprogress"��Ĭ�ϵĶ�������
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
			//ִ��
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
                //�����Զ�����
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
<!--};-->֧���ַ���
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
	// �ȴ����ж��������ڿ�ʼ����$(index).promise().done(function(){})
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