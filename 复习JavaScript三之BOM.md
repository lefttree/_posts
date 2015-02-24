title: 复习JavaScript三之BOM
date: 2015-01-24 22:14:04
tags: JavaScript
---
BOM提供了很多对象，它的核心是window，表示它是浏览器的一个实例，在ECMAScript中又是Global对象。它提供了很多访问浏览器的功能，这些功能与网页无关，所以缺少事实标准的BOM既有意思又有些坑。复习它，主要是复习几个比较有用的对象，其他可以了解一二。

###location

> 算起来它是我用的最多的一个对象

它提供了当前窗口加载的页面有关的信息，也对URL进行了片段分解，既是window的属性，也是document的属性。

* hash 返回URL的散列（#号后面跟着的零个或多个值）
* host 返回服务器名称和端口号
* hostname 返回不带端口号的服务器名称
* href 返回当前加载页面的完整URL
* pathname 返回URL中的目录或文件名
* port 返回URL中指定的端口号
* protocol 返回页面使用的协议
* search 返回URL中的查询字符串，它以问好（?）开头

上述的属性基本上都可以直接使用，search除外，它返回的是一个完整的查询字符串，没有办法访问其中的每个查询字符串参数，还需要额外的进行处理。

一般来说根据它的特点，?开头&拼接，key=value的形式来展现，最好是key和value都要decodeURIComponent一下。

在location中除了上述的属性外，还有一些比较有用的方法和技巧，主要是用来控制页面跳转的问题。

* assign方法接收一个参数，表示立即打开一个新的页面并在历史纪录中生成一条记录，它的效果等同于window.location.href = ''或者location.href = ''
* 修改location对象的属性比如href，hash，search等也可以来改变URL
* replace方法接收一个参数，既跳转到新的URL上，并且不会在历史纪录中增加一条新的纪录
* reload表示重新加载当前页面

<!--more-->

###处理框架，设置时间，open，窗口位置，窗口大小

> open现在估计没人会用了

如果页面中包含框架，则每个框架都有自己的window对象，可以使用frames来获取，比如frames[0]或者frames['name']。这里还要了解的是top，parent，对于这些只要理解的层级关系，每一个指向都是会非常清楚的。

在做某些动画效果的时候，主要是针对PC端，可能会使用到窗口位置，窗口大小的属性来进行计算，比如innerWidth，innerHeight，outerWidth，outerHeight，获取到这些尺寸，一般会与当前div的高宽进行减法来获取精准的位置。

setTimeout和setInterval是进行时间调度的函数，我们知道Js是单线程的，但是可以使用这个在特定的时间范围内执行代码，前面一个setTimeout是在指定的时间内执行（只执行一次），后面的setInterval则是以指定的时间重复执行（N次）

###navigator

用这个一般是在统计用户浏览器版本，操作系统等场景下才用的上，偶尔有几个会比较实用。

* cookieEnabled 判断cookie是否开启
* userAgent 浏览器用户代理字符串
* plugins数组 主要是用来检测浏览器安装的插件

###screen

在Js中有几个对象在编程里真用不上，这个就是其中之一。它主要是用来表明客户端的能力，比如显示器的信息，像素，高，宽等。

###history

history对象保存着用户上网的历史纪录，但是这个也是非常不常用。主要是用go方法，back方法，forward方法。

> 说实话，后面三个navigator,screen,history基本上很废材


