title: 复习JavaScript九之客户端存储方案
date: 2015-02-03 17:43:48
tags: JavaScript
---
随着Web应用越来越复杂，总是有很多需求需要在客户端存储一些特定的信息，用来描述访问者或者跟访问者相关的信息。于是，就有了这些客户端存储的方案，供开发者选择以及使用。

###cookie

说起来cookie的出现是非常早的，用于在客户端存储会话信息，它要求服务器对任意的HTTP请求发送Set-Cookie HTTP头作为响应的一部分，例如：

	HTTP/1.1 200 OK
	Content-Type: text/html
	Set-Cookie: name=value

cookie虽然好用，但是它在客户端上有很多限制，而去获取也不是很容易。最重要的是关于请求的性能，cookie是对任意HTTP设置，所以也会发送给服务器，这些额外信息有时候不是必需的。

* cookie是跟特定的域有关系的，也就意味着存储在cookie中的信息只能让特定的域来访问
* cookie的条数是有限制的，大体上来说各浏览器不会超过50个，除了safair chrome
* cookie在客户端只有一个方法可以用document.cookie
* cookie的大小在客户端上也有限制，大体不会超过1MB
* 如果在客户端设置了cookie，是没法删除的，只能在同一个cookie上设置name=空字符串或者设置失效时间
* 如果设置了secure，cookie只能在SSL链接才能传输

cookie在浏览器上的保存是由几个方面组成的，理解这个对于使用Js操作cookie是一个必备条件，它由名称也就是每一项的key，每一项的值value，域名的限制，路径对于哪一个页面起作用，时间，以及安全标识secure。

在客户端主要是使用document.cookie来进行操作，获取和设置都是这一个。而去获取是一个全的字符串，还需要进一步的处理才能得到值。如果是设置，也是需要设置一个全的字符串。对于这个，最好都要encodeURIComponent一下，获取的时候decodeURIComponent一下。

	var setCookie = function(name,value,expires,path,domain,secure){
		var cookieText = encodeURIComponent(name)+'='+encodeURIComponent(value);
		
		//各项的拼接
		document.cookie = cookieText;
	}

聪明的开发者为了绕开浏览器上对于条数的限制，研究出了一个方式，把真实的值设置在子cookie上。

	name=name=value

###IE上的客户端存储userData

在IE中引入了一个userData的方案来存储客户端数据，允许每个文档最多128KB，每个域1MB的数据量。想使用它首选在那个元素上应用一个css。

	<div style="behavior:url(#default#userData)"></div>

使用的时候跟设置dom属性一样，唯一有一个不同的地方是最后要调用save方法，告诉它存储在哪个数据仓库上。

	var dataStore = document.getElementById('dataStore');
	dataStore.setAttribute('name','lcepy');
	dataStore.save('MyNameInfo');
	
获取

	dataStore.load('MyNameInfo');
	dataStore.getAttribute('name');
	

###HTML5的客户端存储方案

> 支持度以及使用比较广泛的是Web Storage API，当然客户端还有本地数据IndexDB等等，这些也是可以存储客户端数据，但是它们支持的很少。在做移动端开发时，localStorage的使用是非常频繁的，还要注意一点是个别比如safair浏览器有隐私模式，本地缓存是不起作用的，无法读取，所以要做一些额外的处理。

主要的是两个sessionStorage和localStorage两套API来应用，它们是有区别的，前者只在浏览器运行时存在，也可以跨页面，前提是页面不能关闭。后者是永久存在的，如果用户不删除，那么它永远不会删除。它们的使用方式都是一样的，包括API。至于分配的空间，要看各浏览器的态度的，一般情况下是5MB，还有一点它是跟域走的。

	interface Storage{
		readonly attribute unsigned long legth;
		getter DOMString key();
		getter any getItem();
		setter creator void setItem();
		deleter void removeItem();
		viod clear();
	}

* length 描述Storage对象中存储的key=value数量
* key 方法允许获取一个指定位置的键
* getItem方法根据指定的key返回一个value
* setItem方法将数据value存入指定的位置key
* removeItem根据指定的key来删除数据
* clear 删除全部的数据

使用：

	localStorage.setItem('name','lcepy');
	localStorage.getItem('name');
	localStorage.key(0);
	localStorage.length;
	localStorage.removeItem('name');
	localStorage.clear();
	
另外API中还有一套事件用于本地存储发生改变时的通知

	window.addEventLister('storage',function(){},true);
	
event对象中包含有

* key 存储了被更新或者删除的键
* oldValue 包含了更新之前的数据
* url Storage事件发生的源
* newValue 包含了更新之后的数据
* storageArea 包含一个引用，指向发生改变之前的localStorage或者sessionStorage



	






