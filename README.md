
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


