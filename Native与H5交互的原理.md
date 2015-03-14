title: Native与H5交互的原理
date: 2015-03-14 12:14:50
tags: iOS
---
在做混合应用时，H5与Native通信的重要性，我想每一个H5开发者都非常的清楚。通过它，我们可以很好的借用Native API的优势来实现自己想做的事情，业界有一个非常成熟的框架让大家选择，它就是PhoneGap。混合开发，并不是意味着什么App都可以做，它也有局限性。在写交互原理之前，我想先写写自己的体会。

* 混合开发使用的语言是JavaScript由于系统分配的资源始终有限，在UIWebView这个容器中运行，效率跟原生的差距是非常明显的
* 应用需要解决首次加载白屏的问题，因为资源加载的速度始终有些慢
* 这玩意调试起来很痛苦，不像在web端有chrome的开发者工具，iOS有Xcode
* iOS和android交互始终不一样，但是混合应用提供了一个统一的UI，目前不清楚用户是否能习惯交互
* 关于通信JavaScript到Native是一个异步的过程，而Native到JavaScript是一个同步的过程

那么使用混合应用在什么场景下比较有优势呢？

* 内容驱动型的应用
* 经常需要调整UI的应用
* 经常需要调整业务的应用
* 交互不复杂并且不需要大量计算的应用

总结起来，比如携程的App，因为都是内容驱动，比如购买门票，旅游之类的，又比如淘宝的App，大量商品类的展示，以及一些如图书，查询，工具类的应用，混合开发在这些场景下，有很明显的优势。

言归正传，因为iOS并没有提供原生的调用API，所以当JavaScript跟Native进行通信时，就需要利用一些技巧了。UIWebView这个容器，除了可以加载请求之外，也可以加载本地资源，如果当一个页面发起请求时，是要经过UIWebView的，于是有人会想，我能知道这个URL么？不错，UIWebView可以拦截这个request，并且知道它的全部。那么UIWebView可以通知页面不，这个依然可以。利用这些原理技巧，于是两端通信的问题就解决了。

在UIWebView中实现UIWebViewDelegate协议，就可以拦截到页面端发送的request。

	-(BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
	{
    	NSURL *url = [request URL];
    	if ([[url scheme] isEqualToString:@"test"]) {
        	[_webView stringByEvaluatingJavaScriptFromString:@"window.iOSPhoneName = 'iam lcepy'"];
        	return NO;
    	}
    	return YES;
	}

返回YES表示执行浏览器的默认执行，返回NO表示不执行浏览器的默认执行

而从Native到JavaScript可以利用上stringByEvaluatingJavaScriptFromString方法，执行Js字符串，它就能在UIWebView中执行这一段Js代码。在客户端，我们知道全局的对象是window，如果在客户端需要获取结果，可以把Native执行的结果放到window上。

那么Js这一端如何实现呢？不错，利用的就是隐藏的iframe。

	var requ = $('#requestToNative');
	requ.bind('click',function(){
		var iframe = document.createElement('iframe');
		iframe.style.display = 'none';
		iframe.src = 'test://iOS/userInfo';
		var requs = requ[0];
		requs.appendChild(iframe);
		iframe = null;
		alert(window.iOSPhoneName);
	});

发送这个request让Native进行拦截，协议名可以随便取，当然这个名字要取的有意义。

[例子查看](https://github.com/lcepy/geekObjective-C/tree/master/WebViewNativeToJavaScript)