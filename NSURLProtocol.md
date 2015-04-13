title: NSURLProtocol
date: 2015-04-13 11:38:06
tags: iOS
---

阅读资料：

* [Apple Foundation NSURLProtocol](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLProtocol_Class/index.html)
* [http://nshipster.cn/nsurlprotocol/](http://nshipster.cn/nsurlprotocol/)

正如NSHipster在上篇文章所写的一样，iOS离不开网络，那么这就需要网络编程。cocoa提供了一系列的API来操作网络，比如NSURL，NSURLRequest，NSURLSession等等，以及处理网络的GCD，NSOperation多任务处理。

那么NSURLProtocol，它有什么用呢？官方给它的解释是，可以重新定义已经加载的URL的行为或者定义一个新的URL。换句话来说，就是这个类可以监管应用的网络活动，只要是通过URL来进行的。

NSURLProtocol是一个抽象类，也就是说它需要一个子类继承它才可以正常工作，关于怎么使用可以阅读NSHipster的文章，把子类注册到NSURLProtocol中。

	+ (void)consolelog:(UITextView *)debugText
	{
    	debug = debugText;
    	[NSURLProtocol registerClass:self];
	}
	
###案例需求，监管应用的URL加载系统，并且支持web client console log

案例可以在Github中查看[MFLog](https://github.com/lcepy/MFLog)

	+ (BOOL)registerClass:(Class)protocolClass

把子类注册到NSURLProtocol中

	+ (void)unregisterClass:(Class)protocolClass
	
把子类从NSURLProtocol中移除

NSURLProtocol提供了一系列的方法来操作一个URL Data，先看其一，一个静态方法。

	+ (BOOL)canInitWithRequest:(NSURLRequest *)request

canInitWithRequest **注意，这些是覆盖父类NSURLProtocol的静态方法**是系统询问，是否处理该请求URL，如果返回NO，不处理。如果返回YES，子类进一步控制。

	+ (NSURLRequest *)canonicalRequestForRequest:(NSURLRequest *)request
	
返回请求的原始request

	- (void)startLoading
	
加载此次请求，可以通过self.request获取当前URL的request对象

	- (void)stopLoading
	
此次请求完成时，可以在这个方法中处理

每一个NSURLProtocol都有一个client属性，这个属性是NSURLProtocolClient。另外，还可以实现NSConnectionDataDelegate协议，把NSURLProtocol与NSURLConnection链接起来，这样就可以在statLoading调用之后，系统将回调NSConnectionDataDelegate中定义的各种方法，这个在自定义时非常有用。