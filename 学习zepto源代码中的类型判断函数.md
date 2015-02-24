title: 学习zepto源代码中的类型判断函数
date: 2015-02-14 22:55:51
tags: JavaScript
---

在平时的开发中对于类型的判断是使用的很多的东西，我们可能需要对数据的类型进行检查，以满足预期的结果，而zepto中也提供了一揽子的解决方案，用于类型的判断，函数，字符串，对象，数组等。

	$.each("Boolean Number String Function Array Date RegExp Object Error".split(" "),	function(i, name) {
    class2type[ "[object " + name + "]" ] = name.toLowerCase()
  	})

其实从这里就可以看的出来zepto使用的是什么方式来检索数据类型，借用的就是Object对象的toString方法把全类型字符串打印出来，然后再根据这些字符串来分别处理。在zepto的源代码中，它没有这么写，Object.prototype.toString.call(obj)，而是直接toString.call(obj)，这是因为这个toString是一个内置对象，作用域我想我们都明白，在客户端它是如何搜索的。

在得到了全类型的字符串后，可以去class2type的map中取出值来进行比对，来返回正确的类型。

	function isObject(obj)     { return type(obj) == "object" }
	function isFunction(value) { return type(value) == "function" }
	function type(obj) {
    return obj == null ? String(obj) :
      class2type[toString.call(obj)] || "object"
  	}

数组zepto使用的最新的API，Array提供的isArray方法。

	isArray = Array.isArray ||
      function(object){ return object instanceof Array }

zepto还提供了检测document，window等。

	function isWindow(obj)     { return obj != null && obj == obj.window }
  	function isDocument(obj)   { return obj != null && obj.nodeType == obj.DOCUMENT_NODE }
  	function isPlainObject(obj) {
     //getPrototypeOf 返回一个对象的原型 ，这里是不是跟Object.prototype相等
     return isObject(obj) && !isWindow(obj) && Object.getPrototypeOf(obj) == Object.prototype
    }

