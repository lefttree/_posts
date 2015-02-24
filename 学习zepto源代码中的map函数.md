title: 学习zepto源代码中的map函数
date: 2015-02-16 21:57:58
tags: JavaScript
---
在学习map函数之前，先学习几个比较有用的方法，比如判断一个对象是否是空对象，给字符串两边去空白等。判断空对象，主要是利用了in语法，它可以遍历对象的属性，如果是空对象for循环会直接跳过。

	$.isEmptyObject = function(obj) {
    	var name
    	for (name in obj) return false
    	return true
  	}

判断一个元素是否在数组中，借用了在最上面申明的一个空数组的indexOf。

	$.inArray = function(elem, array, i){
    	return emptyArray.indexOf.call(array, elem, i)
  	}
  	
给字符串左右两边去空白，这个是借用了String包装对象的trim方法。

	$.trim = function(str) {
    	return str == null ? "" : String.prototype.trim.call(str)
  	}
 
map函数是一个很有意思很有用的函数，它可以帮助开发者进行映射到每一个元素上，新版的数组也提供了map方法，考虑到某些浏览器没有实现这个，所以zepto帮助我们实现了它。

	$.map = function(elements, callback){
    var value, values = [], i, key
    if (likeArray(elements))
      for (i = 0; i < elements.length; i++) {
        value = callback(elements[i], i)
        if (value != null) values.push(value)
      }
    else
      	for (key in elements) {
        	value = callback(elements[key], key)
        	if (value != null) values.push(value)
      	}
    	return flatten(values)
  	}
  	
likeArray是判断了下length属性是不是number，如果是，遍历，然后装载到values数组中，如果是对象就用for in，最后的flatten是借用一下concat，copy一个副本出来返回。