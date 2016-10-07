

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

}
```

