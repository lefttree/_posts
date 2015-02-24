title: 复习JavaScript八之操作XML
date: 2015-02-02 17:50:14
tags: JavaScript
---
在以前XML是存储和传输数据的事实标准（客户端没有出现JSON之前），那个时候浏览器还不支持解析XML，于是很多开发者需要自己写一个XML解析器。原理，就是把XML也转化成DOM文档，然后通过DOM API来获取。

而现在，在客户端（浏览器）编程中几乎很少会使用到XML，数据的交换都应用在JSON上了，了解这个有一个好处，就是在做服务端编程，比如SAOP协议时就会用到，还有桌面的客户端，或者是移动客户端上，比如iOS，它把一些数据，比如NSArray写入到本地，实际上就是一个XML文本，当然在代码层面上，是看不出来有解析的，如果读取这个文件，是直接转成NSArray对象的。

> IE有它自己的实现对XML的支持，下面说的两个类型，在IE中都不支持

将XML字符串序列化成DOM文档，主要是使用DOMparser类来实现，其他的用法跟获取DOM节点一样。

	var parser = new DOMparser();
	var xmlDomObject = parser.parserFromString('<root><child/></root>','text/xml');
	
	xmlDomObject.getElementsByTagName('child');
	
将DOM文档反序列化成XML字符串，主要是使用XMLSerializer类型，使用上述的xmlDomObject对象。

	var serializer = new XMLSerializer();
	var xml = serializer.serializeToString(xmlDomObject);
	
如果XML解析有错误都会返回一个parsererror标签，获取的时候就可以判断是否有这个标签来决定解析是否有错误。

据说DOM3版本的XML操作只有一个浏览器实现了，所以当资料了解就好。

###IE版本

> IE版本的XML解析主要是使用它的ActiveXObject类型来实现

在IE中想要解析XML就必须先创建一个空的DOM文档，然后通过这个文档来获取。

	var xmldom = createDocument();
	xmldom.loadXML('<root><child/></root>');
	
	xmldom.getElementsByTagName('child');

如果解析有错误，可以检查parseError对象。

	if(xmldom.parseError !== 0){
	    //do 
	}
	
在IE中进行序列化非常的方便，因为它都内置在了DOM文档中，每一个DOM节点都有一个xml属性，它就保存着XML字符串。

	xmldom.xml

最后IE还提供了一个可以加载远程XML文件的方法load，它就接收一个参数，远程XML的URL地址。当然，也可以使用async属性来设置同步还是异步，默认是异步（true）。

	var xmldom = createDocument();
	xmldom.async = false;
	xmldom.load('http://xxx.sss.xml');
	
如果是异步还要监听一下onreadystatechange事件，判断readystate状态，这个跟Ajax的使用是一样的。