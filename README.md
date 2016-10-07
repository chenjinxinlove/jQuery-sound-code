
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


