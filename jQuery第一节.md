
# Jquery 2.0.3 源码分析笔记

1、框架分析
```
(function(window, undefined){
    (21, 94)  定义了一些变量个函数 jQuery = function(){};
    (96, 283) 给jq对象，添加一些对象和方法；
    (285,347)extend : jq的继承方法;
    (349,817) jQuery.extend()  扩展一些工具方法  $.trim() ;
    (877, 2856) sizzle :复杂选择器;
    (2880, 3042) callbacks ：回调对象 : 函数的统一管理
    (3043, 3183) deferred 延迟对象：对异步的统一管理
    (3184,3295) support ： 功能检测的方法
    (3308,3652) data(): 数据缓存
    (3653,3797) queue() :队列管理
    (3803,4299) attr() prop() val() addClass() 对元素属性的操作
    (4300,5128) on() trigger() 对事件操作的相关方法
    (5140,6057) DOM操作的方法
    (6058,6620) css() 样式的操作
    (6621,7854) 提交数据个ajax的操作
    (7855,8584) animate() 运动的方法
    (8585,8792) offset()等，位置与尺寸的方法
    (8804,8821) 支持模块化
    
    (8826) window.jQuery = window.$ = jQuery;
    
})(window)
```
---
2、自执行的实现
```
(function(window, undefined){


})(window)
1、传入window，加快查找速度，作用域链。
2、在压缩版本中，可以进行压缩，用e等来替换

1、undefined可以被修改，传入就会不会被修改。

```
---
3、定义一些变量

```
//提前定义，便于压缩，和理解
rootjQuery,
readyList, 
// 兼容ie9等
core_strundefined = typeof undefined, 

//提前存储，便于压缩
location = window.location,
document = window.docment,
docElem  = document.documentElement，

//防止冲突
_jQuery = window.jQuery,
$ = window.$  ,

//用于$.type()的使用 {'[Object String]':'string,'Object Array':'array'}
class2type = {},

//存储数组
core_deletedIds = [],

//版本
core_version = "2.0.3" ,

//存在方法  es5的方法   ' fff '.trim()
core_concat = core_deletedIds.concat,
core_push = core_deletedIds.push,
core_slice = core_deletedIds.slice,
core_indexOf = core_deletedIds.indexOf,
core_toString = core_deletedIds.toString,
core_hasOwn = class2type.hasOwnProperty,
core_trim = core_version.trim,

```
---
4、面向对象的技巧

```
jQuery = function(selector, context) {
    return new jQuery.fn.init( selector, context, rootjQuery);
}
<!--解释：-->
    <!--(function (window, undefined) {-->
    <!--    var aa = function () {-->
    <!--        return new aa.prototype.init();//返回aa的init方法，但是现在aa的prototype指向了init-->
    <!--    }-->
    <!--    aa.prototype.init = function () {-->
    <!--    }-->
    <!--    aa.prototype.dd = function () {-->
    <!--        console.log('sss')-->
    <!--    }-->
    <!--    aa.prototype.init.prototype = aa.prototype;//修正aa的prototype的指向-->
    <!--    window.aa = aa;-->
    <!--})(window)-->
    <!--aa().dd(); //dd()就可以直接调用了-->
```
---
5、一些正则
```
//匹配数字
core_pmun = /[+-]?(?:\d*\.|)\d+(?:[eE][+-]?\d+|)/.source, 

//匹配单词
core_rnotwhite = /\S+/g,

//前面匹配html标签后面匹配id,并且防止xss攻击
rquickExpr = /^(?:\s*(<[\w\W]+>)[^>]*|#([\W-]*))$/,

// 匹配独立的tag   <p></p>
rsingleTag = /^<(\w+)\s*\/?>(?:<\/\1>|)$/,

//前缀和，但是ie中ms要Ms 那样m要大写
rmsPrefix = /^-ms-/,
//转换margin-left 转成marginLeft,数字-2d转成2d
rdashAlpha = /-([\da-z])/gi,

```
---
6、两个回调函数

```
//转驼峰的回调函数
fcamelCase = function(all,letter){
    return letter.toUpperCase();
}
//dom加载成功之后触发的
completed = function(){
    document.removeEventListenter("DOMContentLoaded", completed, false);
    window.removeEventListenter("load", completed, false);
    jQuery.ready();
}


```
# ~~(21, 94)  定义了一些变量个函数 jQuery = function(){};~~
---
7、jQuery上添加的属性与方法

```
jQuery.fn = jQuery.prototype = {
    jquery : 版本
    construction ： 修正指向问题
    init() : 初始化和参数管理
        //1、如果$(""),$(null),$(undefined),$(false)
        //2、传入是字符串的类型，比如$('#id') $('.class') $('div')创建等等 
    selector ： 存储选择字符串
    lenght : this对象的长度
    toArray() : 转数组
    get() : 转原生集合
    pushStack() : JQ对象的入栈
    each() : 遍历集合
    ready() : DOM加载的接口
    slice() : 集合的截取
    first() : 集合的第一项
    last() : 集合的最后一项
    eq() : 集合的指定项
    map() : 返回新的集合
    end() : 返回集合前一个状态
    push() : (内部使用)
    sort() : (内部使用)
    splice() : (内部使用)
}
```

```
jQuery.fn = jQuery.prototype = {
    //版本
    jquery : core_version,
    //修正指向问题
    constructor : jQuery,
---
    解释说明：
    1、function a() {}
       var b = new a();
    //输出function a() {} 因为在创建a的时候，js源码自动添加a.prototype.constructor = a;
      console.log(b.constructor);
    2.function a() {}
    //但是constructor属性容易被修改，比如
    //a.prototype.constructor = Array;
    var b = new a();
    //function Array() { [native code] }
    //console.log(b.constructor);
    3.function a() {}
    // 在面向对象的编程中使用：
    a.prototype = {
     name:'sss',
     age:44
    }
    var b = new a();
    //function Object() { [native code] } 就需要修正
    console.log(b.constructor);
---
    //初始化和参数管理
---
    **说明一下$()选到的和原生选择器的关系**
    $('li')   它选择到了所以li，是存在 
    this = {
        0：'li',
        1: 'li',
        2: 'li',
        length :3
    }
    然后进行其他的操作，比如css();
    for(var i = 0; i < this.length; i++){
        this[i].style.background = 'red';
    }
    同时$('li')[1]就是原生的对象了。就可以进行操作了。
    $('li')[1].style.background = 'red';
---
    //第一个就是选择器，第二个是第一个的执行环境，是上下文。比如$('li','#div')就是#div下面的li，不会去找其他的li 
    //api：jQuery( selector [, context ] )
    init : function(selector, context, rootjQuery) {
        var match, elem;
        //1、如果$(""),$(null),$(undefined),$(false)
        if( !selector ){
            return this;
        }
        //2、传入是字符串的类型，比如$('#id') $('.class') $('div')创建等等 
        if(typeof selector === "string") {
        
        //如果是<li>等html标签就走哪里
            if(selector.charAt(0) === "<" && selector.charAt(selector.length)-1) === ">" && selector.length >= 3){
                match = [null, selector, null]
        //$('<li>')  =>   match = [null, '<li>', null]
        //$('<li>1</li><li>2</li>')  =>   match = [null, '<li>1</li><li>2</li>', null]
            
            }esle{
                match = rquickExpr.exec(selector);
        //$('.class') $('div') $('#div div.class') => match = [null]
        //$('#div')  => match = ['#div', null, 'div'];
        //$('<li>Heool') => match = ['<li>Heool', '<li>', null]
            }
            //创建便签或者#id
            if(match && (match[1] || !context)){
                //创建便签
                if(match[1]){
           //api : jQuery( html [, ownerDocument ] ) 可以改变创建的环境，比如在ifrme中
           //$('<li>',document)就直接返回，也支持$('<li>',$(document))就是返回contex[0]
           //还是返回documen
                context = context instanceof jQuery ? context[0] : context;
                //jQuery.parseHTML()可以把字符串创建为DOM节点的数组
                //jQuery.merge() 数组的合并   
                jQuery.merge(this,jQuery.parseHTML(
                    match[1],
                    //是哪个页面，本页面或者iframe
                    context && context.nodeType ? context.ownerDocument || context : document,
                    //默认是false，但是true是可以创建script
                    true
                ))
 //创建html直接加属性的$('<div>',{name:'dddd','html':'ddd',id:'ddddd'}).appendTo('#div')
 
 //只能使用单标签，参数对象自变量的形式
                if(rsingleTag.test(match[1]) && jQuery.isPlainObject(context)){
                    for(match in context) {
                    
                    //比如是html那个方法，jquery有，就会$().html('ddd')
                        if(jQuery.isFunction(this[match])){
                            this[match](context[match])
                        }else{
                            this.attr(match, context[match]);
                        }
                    }
                }
                //结束
                return this;
    
            //选择#id
            }else{
                elem = docment.getElementById(match[2]);
                //兼容黑莓双重判断
                if(elem && elem.parentNode){
                    this.length = 1;
                    this[0] = elem;
                }
                
                this.context = document;
                this.selector = selector;
                 //结束
                return this;
            }
            
            //  jQuery(document).find()
        }else if(!context || context.jquery){
            //find 就是调用 sizzle
            return (context || rootjQuery).find(selector);
        }else{
           //如果上下文不为空，就在上下文中寻找
            return this.constructor(context).find(selector);
        }
    //string的else
    //节点类型
    } else if(selector.nodeType){
    //上下文就是本身
        this.context = this[0] = selector;
        this.length = 1;
        return this;文档加载  
        //function类型，就是 $(function(){})  简写 $(document).ready()
    } else if(jQuery.isFunction(selector)){
        return rootjQuery.ready(selector);
    }
    //$($('#div')) 处理那种情况
    if(selector.selector !== undefined){
        this.selector = selector.selector;
        this.context = selector.context;
    }
    //处理数组和对象
    //makeArray就是把不是数组的转成数组，就是es6的form方法
    //内部使用时，传入this（特殊的Object）例如{lenght:1}
    <!--var lis = document.getElementsByTagName('li');-->
    <!--console.log($.makeArray(lis),{length:5})-->
      return jQuery.makeArray(selector, this)    
    },
    
    //一个空的选择元素
    selector : "",
    //初始的长度0
    lenght : 0, 
    
    toArray : function(){
        return core_slice.call(this);
    },
    //转原生集合 可以得到原生的Dom,然后直接使用原生的方法。没穿就是转成数组，穿就是得个对应的节点
    get : function(num){
        return num == null ?
        
        this.toArray():
        //负数就长度加数
        (num < 0 ? this[this.length + num] : this[num] );
    },
    //JQ对象的入栈(先进先出)
    pushStack : function(elems){
        var ret = jQuery.merge(this.constructor(), elems);
        //在新的属性上，挂载prevObject的属性，那个属性指向之前的对象，就是下一个栈
        ret.prevObject = this;
        ret.context = this.context;
        return ret;
    },
    //遍历集合
    //工具方法是最底层，实例方法调用工具
    each : function(callback, args){
        return jQuery.each(this, callback, args);
    },
    //DOM加载的接口
    ready : function(fn){
        jQuery.ready.promise().done(fn);
        return this;
    },
    //集合的截取
    //$('li').slice('1','3').css('background','red').end().css('color','blue')
    slice : function(){
        return this.pushStack(core_slice.apply(this, argument));
    }
    //返回集合前一个状态
    //返回栈的前一个，或者返回null就是没有了。
    first : function(){
        return this.eq(0);
    },
    last : function(){
        return this.eq(-1);
    },
    eq : function(i){
        var len = this.length,
        //正加0，负加长度
        j = +i + (i<0 ? len : 0);
        return this.pushStack(j >= 0 && j < len ? [this[j]]:[]);
    },
    map : function(callback){
        return this.pushStack(jQuery.map(this,function(elem, i){
            return callback.call(elem, i, elem)
        }))
    },
    end : function(){
        return this.preObject || this.constructor(null);
    },
    push : core_push,
    sort ： [].sort,
    splice : [].splice
}
```

---
~~# (96, 283) 给jq对象，添加一些对象和方法~~

