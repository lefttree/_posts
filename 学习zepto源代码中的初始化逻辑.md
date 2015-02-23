title: 学习zepto源代码中的初始化逻辑
date: 2015-02-15 14:11:30
tags: node&js
---
基本上是个人都知道在使用zepto时，是$符号来获取元素对象，如果是空会返回一个数组。从源代码上面来看，是这样的。

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
