# jQuery-sound-code
---
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
rootjQuery,
readyList, //提前定义，便于压缩，和理解
core_strundefined = typeof undefined, // 兼容ie9等

location = window.location,
document = window.docment,
docElem  = document.documentElement，//提前存储，便于压缩

_jQuery = window.jQuery,
$ = window.$  ,//防止冲突

class2type = {},//用于$.type()的使用 {'[Object String]':'string,'Object Array':'array'}

core_deletedIds = [],//存储数组

core_version = "2.0.3" ,//版本

core_concat = core_deletedIds.concat,
core_push = core_deletedIds.push,
core_slice = core_deletedIds.slice,
core_indexOf = core_deletedIds.indexOf,
core_toString = core_deletedIds.toString,
core_hasOwn = class2type.hasOwnProperty,
core_trim = core_version.trim,//存在方法  es5的方法   ' fff '.trim()

jQuery = function(selector, context) {
    return new jQuery.fn.init( selector, context, rootjQuery);
}





```