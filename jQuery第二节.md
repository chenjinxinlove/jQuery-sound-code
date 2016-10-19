

```
//修正原型指向
jQuery.fn.init.prototype = jQuery.fn;

//jq的继承的方法，实例方法和工具方法
--------使用说明：----------------------------------------------
<!--1、写一个对象-->
<!-- $.extend({-->
<!--        aa:function () {-->
<!--            alert('111');-->
<!--        }-->
<!--    })-->
<!--    $.fn.extend({-->
<!--        aa:function () {-->
<!--            alert('222');-->
<!--        }-->
<!--    })-->
<!--    $.aa();-->
<!--    $().aa();-->
--------------------------------------------------------------
<!--2.写多个对象，后面都是向前一个扩展，assgin  es6的方法相似-->
<!--     aa = {"name":"dddd"},-->
<!--            bb = {"age":44}-->
<!--    $.extend(aa,bb);-->
<!--    console.log(aa,bb)-->
--------------------------------------------------------------
<!--3.默认是浅拷贝-->
<!--  在第一个参数的位置使用深拷贝-->
<!--  $.extend(true,aa,bb);-->
-------简化版本-----------------------------------------------
jQuery.extend = jQuery.fn.extend = function(){
    定义一些变量
    if(){} 判断是不是深拷贝
    if(){} 判断参数是否正确
    if(){} 判断是不是插件的形式
    for(){可能有多个对象情况
        if(){} 防止循环引用
        if(){} 深拷贝
        else if() {} 浅拷贝
    }
}
-------源码---------------------------------------------------
jQuery.extend = jQuery.fn.extend = function(){
    //定义一些变量
    var options, name, copy, copyIsArray, clone,
    //目标元素
    target = argument[0] || {},
    i = 1,
    length = arguments.length,
    //深拷贝默认fasle
    deep = false;
    //判断是不是深拷贝
    if( typeof terget === "boolean"){
        //修改深拷贝
        deep = target;
        //修改目标元素的指向
        target = argument[1] || {};
        i = 2;
    }
    //判断参数是否正确
    if(typeof target !=="object" && !jQuery.isFunction(target)){
        target = {};
    }
    //判断是不是插件的形式
    if(length === i ){
        target = this,
        --i;
    }
    //可能有多个对象情况
    for(;i < length; i++){
    //不等于null的，做处理，过滤只用
        if((options = arguments[i]) != null){
            for(name in options) {
                src = target[name];
                copy = options[name];
                //防止循环引用
                if(target === copy){
                    continue;
                };
                //深拷贝
                if(deep && copy && (jQuery.isPlainObject(copy)
                || (copyIsArray = jQuery.isArray(copy))){
                    if(copyIsArray){
                        copyIsArray = false;
                        clone = src && jQuery.isArray(src) ? src : [];
                    }else{
                        clone = src && jQuery.isPlainObject(src) ? src : {};
                    }
                    //利用递归
                    target[name] = jQuery.extent(deep, clone, copy);
                
                //浅拷贝
                }else if(copy !== undefined){
                    target[name] = copy;
                }
            }
        
        }
    }
    return target;

}
```

//jquery拓展工具方法

```

-------简化版本---------------------------------------------------------------


jQuery.extend({
    
    expando :　生成唯一jq字符串（内部）
    noConflict() : 防止冲突
    isReady : DOM是否加载完(内部)
    holdReady() : 推迟Dom加载
    ready() : 准备dom触发
    isFunction : 是否为数组
    isArray() : 是否是组数
    isWindow() : 是否是window
    isNumberic() : 是否是为数字
    type() : 判断数据类型
    isPlainObject() : 是否为对象自变量
    isEmptyObject() : 是否为空的对象
    error() : 抛出异常
    parseHTML() : 解析异常
    parseJSON() : 解析JSON
    parseXML() : 解析XML
    noop() : 空函数
    globalEval() : 全局解析JS
    camelCase() : 转驼峰
    nodeName() : 是否为指定节点名(内部)
    each() : 遍历集合
    trim() : 去前后空格
    makeArray() : 类数组转真数组
    inArray() : 数组版indexof
    merge() : 合并数组
    grep() : 过滤新数组
    map() : 映射新数组
    guid : 唯一标示符(内部)
    proxy() : 改变this的指向
    access() : 多功能值操作(内部)
    now() : 当前时间
    swap() : css交换(交换)

})
------------------------------------------------------------------------------

jQuery.extend({
    //生成唯一jq字符串（内部）
    expando: "jQuery" + (core_version + Math.random()).replace(/\D/g,""),
    //防止冲突
    ---使用说明----
    1、
    <!--var dd = $.noConflict();-->
    <!--$ = 123;-->
    <!--dd(function () {-->
    <!--    //123-->
    <!--    console.log($)-->
    <!--})-->
    2、
    <!--$ = 123;-->
    <!--script src='jquery'-->
    <!--var dd = $.noConflict();-->
  
    <!--dd(function () {-->
    <!--    //123-->
    <!--    console.log($)-->
    <!--})-->
    ----------
    noConflict:function(deep){
        //解决第二种
        //在代码上面有 _$ = window.$  就把123存到了_$
        if(window.$ === jQuery){ 
        //放弃$
            window.$ = _$;
        }
        //放弃jQuery那个对外接口
        //_jQuery = window.jQuery
        if(deep && window.jQuery === jQuery){
            放弃jQuery
            window.jQuery = _jQuery;
        }
        //那是解决$出现jquery之下（1）
        return jQuery;
    },
    //DOM是否加载完(内部)
------DOM加载说明-------------------------------------------------------------------
1、$(function(){}) 和 window.onload = function(){} 的区别
前者是dom加载完，后者是所有的东西都加载完
2、利用原生DOMContentLoaded
3、$(function(){})  => $(documnet).ready(function(){}) => $().ready()

|=> jQuery.ready.promise().done(fn) => 监听DOMContentLoaded =>$.ready() => fn

4、

jQuery.ready.promise = function( obj ) {

    //第一次可以进来，之后就进不来了，加载一次
	if ( !readyList ) {

		readyList = jQuery.Deferred();
		//dom已经加载完，就不用再加载。readyState是标示的状态
		if ( document.readyState === "complete" ) {
		//ie会提前触发，加延时
			setTimeout( jQuery.ready );
		} else {
		//有些浏览器会缓存load，所有那个先加载完成就使用那个都可以
			document.addEventListener( "DOMContentLoaded", completed, false );
			window.addEventListener( "load", completed, false );
		}
		//取消事件，那个没加载完就直接取消
		<!--completed = function() {-->
		<!--document.removeEventListener( "DOMContentLoaded", completed, false );-->
		<!--window.removeEventListener( "load", completed, false );-->
		<!--jQuery.ready();-->
	};
		
	}
	return readyList.promise( obj );
};
------------------------------------------------------------------------------------   
    isReady: false,
    readyWait: 1,
    //holdReady(true) 推迟加载 holdReady(false) 释放加载
-----使用说明-----------------------------------------------------------------------
1、在异步加载的事件使用
a.js  alert('2')
$.getScript('a.js',function(){

})
alert('1');
先弹出1，在弹出2因为异步

$.holdReady(true);
$.getScript('a.js',function(){
    $.holdReady(false);
})
alert('1');
就是2 1 的弹出顺序
------------------------------------------------------------------------------------    
	holdReady: function( hold ) {
		if ( hold ) {
			jQuery.readyWait++;
		} else {
			jQuery.ready( true );
		}
	},
	//wait 就是上面的true
	ready: function( wait ) {
	   //jQuery.readyWait不为0就返回
		if ( wait === true ? --jQuery.readyWait : jQuery.isReady ) {
			return;
		}

		jQuery.isReady = true;
        //
		if ( wait !== true && --jQuery.readyWait > 0 ) {
			return;
		}
        //不到参数来到那，相当于把 document, [ jQuery ]传给fn
		readyList.resolveWith( document, [ jQuery ] );
        
        //$(documet).on('ready',function(){})的写法
		if ( jQuery.fn.trigger ) {
			jQuery( document ).trigger("ready").off("ready");
		}
	},
	//判断数函数吗
	isFunction : function(){
	   return jQuery.type(obj) === "function";
	}
	isArray : Array.isArray,
	//是否是window
	isWindow : function(obj){
	    return obj != null && obj === obj.window;
	},
	//是否是为数字
	isNumberic : function(obj){
	    //把obj转成数字，不是就转成NaN就是true，！true；isFinite判断是有限数2的53
	    return !isNaN(parseFloat(obj) && isFinite(obj)
	},
	判断数据类型
	type : function(obj){
	    //null 或者 undefined
	    if ( obj == null ) {
			return String( obj );
		}
		// Support: Safari <= 5.1 (functionish RegExp)
		return typeof obj === "object" || typeof obj === "function" ?
			class2type[ core_toString.call(obj) ] || "object" :
			typeof obj;
<!--		jQuery.each("Boolean Number String Function Array Date RegExp Object Error".split(" "), function(i, name) {-->
<!--	class2type[ "[object " + name + "]" ] = name.toLowerCase();-->
<!--});-->
<!--	}-->
    //是否为对象自变量
	isPlainObject : function(){
	//不等于，DOM节点，不是window
	    if(jQuery.type(obj) !== "object" || obj.nodeType || jQuery.isWindow(obj)){
	        return false;
	    }
	    try {
			if ( obj.constructor &&
					!core_hasOwn.call( obj.constructor.prototype, "isPrototypeOf" ) ) {
				return false;
			}
		} catch ( e ) {
			return false;
		}

		// If the function hasn't returned already, we're confident that
		// |obj| is a plain object, created by {} or constructed with new Object
		return true;
	},
	//是否为空的对象
	isEmptyObject: function( obj ) {
		var name;
		for ( name in obj ) {
			return false;
		}
		return true;
	},
    //报错
	error: function( msg ) {
		throw new Error( msg );
	},
	//解析html  第三个参数keepScripts设为true就可以存script
	parseHTML: function( data, context, keepScripts ) {
		if ( !data || typeof data !== "string" ) {
			return null;
		}
		//省略参数
		if ( typeof context === "boolean" ) {
			keepScripts = context;
			context = false;
		}
		context = context || document;
        //判断是单标签
		var parsed = rsingleTag.exec( data ),
			scripts = !keepScripts && [];

		// Single tag
		if ( parsed ) {
			return [ context.createElement( parsed[1] ) ];
		}

		parsed = jQuery.buildFragment( [ data ], context, scripts );

		if ( scripts ) {
			jQuery( scripts ).remove();
		}

		return jQuery.merge( [], parsed.childNodes );
	},
	//解析json
	parseJSON: JSON.parse,
	//解析xml
	parseXML : function(data){
	    var xml, tmp;
	    if(!data || typeof data !== "string"){
	        return null;
	    }
	 <!--如果浏览器支持window.DOMParser对象，则直接用它的parseFromString()方法加载xml字符串。IE浏览器不支持window.DOMParser，则用loadXML()加载。-->
	    try{
	        tmp = new DOMParser();
	        xml = tmp.parseFromString(data, "text/xml")
	    }catch(e){
	    //ie下查找报错
	        xml = undefined;
	    }
	    //查找报错
	    if(!xml || xml.getElementByTagName("parsererror").lenght){
	        jQuery.error("Invalid XML" + data)
	    }
	    return xml;
	},
	//返回一个空函数
	noop : function(){},
	//把局部变量转成全局
	globalEval ：function(code){
	    var script,
	    //存一下，还可以转成全局
	        indirect = eval;
	       code = jQuery.trim(code);
	       if(code){
	       //严格模式
	            if(code.indexOf("use strict") === 1){
    	          script = document.createElement("script");
    	          script.text = code;
    	          document.head.appendChild(script).parentNode.removeChild(script);
    	         }else{
    	            indirect(code);
    	         }
	       }
	},
	//转驼峰
	//-ms-tr => msTr webkit-tr  WebkitTr
	camelCase() : function(){
	    return string.replace(rmsPrefix, "ms-").replace(rdashAlpha, fcamelCase);
	},
	//判断为指定节点名
	nodeName : function(elem, name){
	    return elem.nodeName && elem.nodeName.toLowerCase() === name.toLowerCase;
	},
	each : function(obj, callback, args){
	    var value,
	        i = 0,
	        length = obj.lenght,
	        isArray = isArraylike(obj);
	        
	        if(args){
	            if(isArray){
	                for(; i < length;i++){
	                    value = callback.apply(obj[i],args);
	                    if(value === false){
	                        breake;
	                    }
	                }
	            }else{
	                for(i in obj){
	                    value = callback.apply(obj[i],args);
	                    if(value === fasle){
	                        break;
	                    }
	                }
	            }
	            
	        }else{
	            if(isArray){
	                for(;i < lenght; i++){
	                    value = callback.call(obj[i],i,obj[i]);
	                    if(value === false){
	                        break;
	                    }
	                }
	            }else{
	                for(i in obj){
	                    value = callback.call(obj[i],i,obj[i]);
	                    if(value === fasle){
	                        break;
	                    }
	                }
	            }
	        
	        }
	        return obj; 
	},
	//去掉空格 str.trim()
	trim : function(text){
	    return text == null ? "" : core_trim.call(text);
	},
	//类数组转真数组 内部使用2个参数 外部使用就1个参数
	makeArray : function(arr, results){
	    var ret = results || [];
	    if(arr !== null){
	        if(isArraylike(Object(arr))){
	            jQuery.merge(ret, 
	                typeof arr === "string"?
	                [arr] : arr
	            );
	        }else{
	        //不是数组比如123 =>[123]
	            core_push.call(ret, arr);
	        }
	    }
	    retrun ret;
	},
	//组数的indexOf版，就是indexOf findIndex
	inArray:function(elem, arr, i){
	    return arr == null ? -1 : core_indexof.call(arr, elem, i)
	},
	//合并数组
	merge : function(first, second){
	    var l = second.lenght,
	        i = first.lenght,
	        j = 0;
	        //不是数组，是转成内部使用的节点
	       if( typeof l === "number"){
	            for(; j < l;j++){
	                first[i++] = second[j];
	            }
	       } else{
	       //jons必须是{0：'c'}
	            while(second[j] !== undefined){
	                first[i++] = second[j++];
	            }
	        
	       }
	       
	       first.lenght = i;
	       return first;
	       
	},
	//过滤得到新数组  filter
	grep : function(elems, callback, inv){
	//第三个参数是true时就是取反
	    var retVal,
	        ret = [],
	        i = 0,
	        lenght = elems.lenght;
	        //不填或者false 得fasle
	        inv = !!inv;
	        
	        for(;i<lenght;i++){
	            retVal = !!callback(elems[i], i);
	            //不等于就添加到数组中，返回
	            if(inv !== retVal){
	                ret.push(elems[i]);
	            }
	        return ret;
	        }
	}，
	//映射成新的组数
	map : function( elems, callback, arg ) {
		var value,
			i = 0,
			length = elems.length,
			isArray = isArraylike( elems ),
			ret = [];

		// array
		if ( isArray ) {
			for ( ; i < length; i++ ) {
				value = callback( elems[ i ], i, arg );

				if ( value != null ) {
					ret[ ret.length ] = value;
				}
			}

		//  key  object,
		} else {
			for ( i in elems ) {
				value = callback( elems[ i ], i, arg );

				if ( value != null ) {
					ret[ ret.length ] = value;
				}
			}
		}

		// 不要复合数组
		return core_concat.apply( [], ret );
	},
	//唯一标识符
	guid: 1,
	//改指向 $.proxy(show,document,3,4)(1,2)  可以两个地方传参
	proxy: function( fn, context ) {
		var tmp, args, proxy;

		if ( typeof context === "string" ) {
			tmp = fn[ context ];
			context = fn;
			fn = tmp;
		}

		if ( !jQuery.isFunction( fn ) ) {
			return undefined;
		}

		args = core_slice.call( arguments, 2 );
		proxy = function() {
			return fn.apply( context || this, args.concat( core_slice.call( arguments ) ) );
		};

		proxy.guid = fn.guid = fn.guid || jQuery.guid++;
		return proxy;
	},
	//多功能值操作(内部)
	access: function( elems, fn, key, value, chainable, emptyGet, raw ) {
		var i = 0,
			length = elems.length,
			bulk = key == null;

		// Sets many values
		if ( jQuery.type( key ) === "object" ) {
			chainable = true;
			for ( i in key ) {
				jQuery.access( elems, fn, i, key[i], true, emptyGet, raw );
			}

		// Sets one value
		} else if ( value !== undefined ) {
			chainable = true;

			if ( !jQuery.isFunction( value ) ) {
				raw = true;
			}

			if ( bulk ) {
				// Bulk operations run against the entire set
				if ( raw ) {
					fn.call( elems, value );
					fn = null;

				// ...except when executing function values
				} else {
					bulk = fn;
					fn = function( elem, key, value ) {
						return bulk.call( jQuery( elem ), value );
					};
				}
			}

			if ( fn ) {
				for ( ; i < length; i++ ) {
					fn( elems[i], key, raw ? value : value.call( elems[i], i, fn( elems[i], key ) ) );
				}
			}
		}

		return chainable ?
			elems :

			// Gets
			bulk ?
				fn.call( elems ) :
				length ? fn( elems[0], key ) : emptyGet;
	},

	now: Date.now,
    //css交换(交换)
	swap: function( elem, options, callback, args ) {
		var ret, name,
			old = {};

		// Remember the old values, and insert the new ones
		for ( name in options ) {
			old[ name ] = elem.style[ name ];
			elem.style[ name ] = options[ name ];
		}

		ret = callback.apply( elem, args || [] );

		// Revert the old values
		for ( name in options ) {
			elem.style[ name ] = old[ name ];
		}

		return ret;
	}

	
})