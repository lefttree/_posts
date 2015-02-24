title: 学习zepto源代码中的each函数
date: 2015-02-17 17:41:49
tags: JavaScript
---

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
