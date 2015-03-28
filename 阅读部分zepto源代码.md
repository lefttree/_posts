title: 阅读部分zepto源代码
date: 2015-02-15 14:11:30
tags: JavaScript
---

zepto初始化时，是$符号来获取元素对象，如果是空则会返回一个数组，从源代码上面来看，是这样的：

	$ = function(selector, context){
    	return zepto.init(selector, context)
  	}

可以传入两个参数，一个selector，一个context，在这个函数内部会调用zepto.init方法。

	zepto.init = function(selector, context) {
    var dom
    // If nothing given, return an empty Zepto collection
    if (!selector) return zepto.Z()
    // Optimize for string selectors
    else if (typeof selector == 'string') {
      selector = selector.trim()
      // If it's a html fragment, create nodes from it
      // Note: In both Chrome 21 and Firefox 15, DOM error 12
      // is thrown if the fragment doesn't begin with <
      if (selector[0] == '<' && fragmentRE.test(selector))
        dom = zepto.fragment(selector, RegExp.$1, context), selector = null
      // If there's a context, create a collection on that context first, and select
      // nodes from there
      else if (context !== undefined) return $(context).find(selector)
      // If it's a CSS selector, use it to select nodes.
      else dom = zepto.qsa(document, selector)
    }
    // If a function is given, call it when the DOM is ready
    else if (isFunction(selector)) return $(document).ready(selector)
    // If a Zepto collection is given, just return it
    else if (zepto.isZ(selector)) return selector
    else {
      // normalize array if an array of nodes is given
      if (isArray(selector)) dom = compact(selector)
      // Wrap DOM nodes.```
      else if (isObject(selector))
        dom = [selector], selector = null
      // If it's a html fragment, create nodes from it
      else if (fragmentRE.test(selector))
        dom = zepto.fragment(selector.trim(), RegExp.$1, context), selector = null
      // If there's a context, create a collection on that context first, and select
      // nodes from there
      else if (context !== undefined) return $(context).find(selector)
      // And last but no least, if it's a CSS selector, use it to select nodes.
      else dom = zepto.qsa(document, selector)
    }
    // create a new Zepto collection from the nodes found
    return zepto.Z(dom, selector)
  
  	}

	zepto.Z = function(dom, selector) {
    	dom = dom || []
    	dom.__proto__ = $.fn
    	dom.selector = selector || ''
    	return dom
  	}

如果什么都不传入，它在init内部会调用Z，并且返回一个空的数组，这个数组的原型会指向$.fn，$.fn是一个字面量的对象，全部是zepto的一些其他方法。

如果context有值，它会指定一个上下文，也就是范围$(context).find(selector)，如果context没有值，则会对selector的值进行检索，如果selector是一个标签字符串，则调用zepto.fragment方法来获取dom对象。如果selector不是标签字符串，则调用zepto.qsa方法，得到DOM对象，在qsa方法中会检查#，.也就是id和class，如果id有返回的是从getElementById中获取的dom对象，如果是class则调用数组的slice方法call一下比如querySelectorAll等，在获取dom对象时，就已经进行了装载，最后调用zepto.Z方法，返回这个数组。

	zepto.qsa = function(element, selector){
    var found,
        maybeID = selector[0] == '#',
        maybeClass = !maybeID && selector[0] == '.',
        nameOnly = maybeID || maybeClass ? selector.slice(1) : selector, // Ensure that a 1 char tag name still gets checked
        isSimple = simpleSelectorRE.test(nameOnly)
    return (isDocument(element) && isSimple && maybeID) ?
      ( (found = element.getElementById(nameOnly)) ? [found] : [] ) :
      (element.nodeType !== 1 && element.nodeType !== 9) ? [] :
      slice.call(
        isSimple && !maybeID ?
          maybeClass ? element.getElementsByClassName(nameOnly) : // If it's simple, it could be a class
          element.getElementsByTagName(selector) : // Or a tag
          element.querySelectorAll(selector) // Or it's not simple, and we need to query all
      )
  	}
  	
  	zepto.fragment = function(html, name, properties) {
    var dom, nodes, container

    // A special case optimization for a single tag
    if (singleTagRE.test(html)) dom = $(document.createElement(RegExp.$1))

    if (!dom) {
      if (html.replace) html = html.replace(tagExpanderRE, "<$1></$2>")
      if (name === undefined) name = fragmentRE.test(html) && RegExp.$1
      if (!(name in containers)) name = '*'

      container = containers[name]
      container.innerHTML = '' + html
      dom = $.each(slice.call(container.childNodes), function(){
        container.removeChild(this)
      })
    }

    if (isPlainObject(properties)) {
      nodes = $(dom)
      $.each(properties, function(key, value) {
        if (methodAttributes.indexOf(key) > -1) nodes[key](value)
        else nodes.attr(key, value)
      })
    }

    return dom
  	}

其实这个数组，就是我们使用到的真正的东西了，在Z方法内部，主要就是两个动作，把dom的原型指向$.fn，把dom的selector属性指向selector，这样就可以使用到zepto中的其他可以使用的方法了。


而平时的开发中对于类型的判断是使用的很多的东西，我们可能需要对数据的类型进行检查，以满足预期的结果，而zepto中也提供了一揽子的解决方案，用于类型的判断，函数，字符串，对象，数组等。

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

each顾名思义是用来遍历的

	$.each = function(elements, callback){
    var i, key
    if (likeArray(elements)) {
      for (i = 0; i < elements.length; i++)
        //函数的默认返回值是undefined ，那么什么情况下会成 false  ?
        if (callback.call(elements[i], i, elements[i]) === false) return elements
    } else {
      for (key in elements)
        if (callback.call(elements[key], key, elements[key]) === false) return elements
    }

    return elements
  	} 

唯一有个问题，看看有其他朋友知晓不，什么情况下callback.call会等于false？按道理来说，如果检查typeof callback === 'function'，这样的情况，我认为可以理解。毕竟函数的默认返回值就是undefined，但是它也不是“强类型”相等的，所以我不明白它的意义何在？

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

read函数，是在开发中非常有用的函数，主要是用来当DOM文档准备好之后，再加载或直接某些Js片段。就是怕，某些情况下，比如Js片段已经写好了，这里面要获取某个DOM节点，实际上这里会是null，然后执行其他操作就抛错误了，因为在浏览器中，这个节点还不存在。

	//读取readyState状态
      if (readyRE.test(document.readyState) && document.body) callback($)
    //监听DOMContentLoaded事件
      else document.addEventListener('DOMContentLoaded', function(){ callback($) }, false)
      return  this
      
zepto因为是移动端的开发，所以它监听的是DOMContentLoaded事件，这个事件是一个原生方法，新版API中提供了这个，而对IE10兼容，则是判断document的readyState属性，保证它是complete|loaded|interactive，这也意味着已经准备好了，并且还要判断body是否存在，才执行callback函数。