title: 复习JavaScript一之Ajax
date: 2015-01-23 18:07:54
tags: JavaScript
---

> IE8已上，支持现代XMLHttpRequest

客户端Js与服务器进行网络交互必备的一个玩意，它不支持跨域，若要跨域还需要进行一些额外的处理。

	var xhr = new XMLHttpRequest();

在使用xhr对象时，要调用的第一个方法是open()，它接受三个参数［发送请求的类型，请求的URL，描述是否同步还是异步的布尔值］false同步，true异步。

关于Ajax同步异步的个人理解：

* 同步，是用数据块的方式来传输的，在Js执行的表现上，当执行到这个Ajax请求时会等待它与服务器交互成功之后才能执行下面一行的代码，也就是阻塞。
* 异步，是用字节来传输的，它不等待是否成功，会执行之后的代码

结束时需要调用xhr.send()，如果没有发送数据的主体，必须要null，做为发送参数。另外在接收到响应之前还可以调用abort()来取消异步请求（不建议调用它）

###HTTP状态验证

当收到响应后会自动填充xhr对象，它有几个比较重要的状态，我们必须要了解清楚与处理。

* responseText:作为响应主体返回的文本
* responseXML:如果响应内容的类型是"text/xml"或者"application/xml"，这个属性中保存的就是XML的DOM文档
* status：响应的HTTP状态
* statusText：HTTP状态的说明
* readyState：用于描述请求发送到完成的过程

正常情况下需要检测status === 200 readyState === 4 这就表示responseText或者responseXML中已经填充了全部的数据可以提供给客户端使用了。

	1 开头的用于描述请求已经发送，需要请求者继续操作才能继续的状态
	2 开头的用于描述请求已经成功
	3 开头的用于描述成功，但是还需要继续操作
	4 开头的用于描述客户端发送了什么数据导致服务器错误
	5 开头的用于描述服务器错误（常见的如，服务端代码抛错了）
	
readyState状态
	
	0 未初始化，还没有调用open方法
	1 已经调用open方法，还没有调用send方法
	2 已经调用send方法，但是还没有接收到响应
	3 已经接收了部分数据
	4 已经接收了全部的数据
	
###xhr对象其他方法或事件

每一个请求和响应都会带有相应的HTTP头信息，其中对开发者是很有用的，而xhr对象提供了一个setRequestHeader方法来设置头信息，它必须在调用open方法之后并且在send方法之前。有设置，必须得有获取，xhr对象也提供了两个方法分别来获取，getResponseHeader传入一个头部字段名来获取，getAllResponseHeaders来获取全部的头信息。

onreadystatechange事件，每次刷新状态时，系统都会重新调用此事件。

 



