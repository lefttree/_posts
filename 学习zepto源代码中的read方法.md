title: 学习zepto源代码中的read方法
date: 2015-03-02 11:31:58
tags: JavaScript
---

read函数，是在开发中非常有用的函数，主要是用来当DOM文档准备好之后，再加载或直接某些Js片段。就是怕，某些情况下，比如Js片段已经写好了，这里面要获取某个DOM节点，实际上这里会是null，然后执行其他操作就抛错误了，因为在浏览器中，这个节点还不存在。

	//读取readyState状态
      if (readyRE.test(document.readyState) && document.body) callback($)
    //监听DOMContentLoaded事件
      else document.addEventListener('DOMContentLoaded', function(){ callback($) }, false)
      return  this
      
zepto因为是移动端的开发，所以它监听的是DOMContentLoaded事件，这个事件是一个原生方法，新版API中提供了这个，而对IE10兼容，则是判断document的readyState属性，保证它是complete|loaded|interactive，这也意味着已经准备好了，并且还要判断body是否存在，才执行callback函数。