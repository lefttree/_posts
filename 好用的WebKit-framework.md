title: 好用的WebKit.framework
date: 2015-05-01 14:25:36
tags: iOS
---

WebKit是iOS8推出的一个用于替代UIWebView的框架，解决了很多UIWebView的问题，比如释放内存，各种稀奇古怪的bug，而且还内置了手势的处理，一句话：好用。

注意：**2015年4月更新的苹果审核文档中明确标识，必须要用WebKit替换UIWebView，不然审核拒绝。**

在使用WebKit框架的过程中，自己也遇到了一些问题，有些解决了，有些还未解决。

* WebKit目前还未能在storyboard中直接使用，需要用代码的方式创建，手动添加约束
* 对于某些页面如果设置了overflow-x，这可能引起scrollView的contentSize的变化，进一步影响WKWebView的内置手势，swipe返回失效
* 目前还未找到怎么处理NSURLCache的问题，也就是页面的缓存机制，看样子好像要自己实现

如何创建一个WKWebView？首先要在link中引入WebKit.framework，然后使用代码的方式：

	-(WKWebView *)WKWebPageView
	{
    	if (!_WKWebPageView) {
        	_WKWebPageView = [[WKWebView alloc] initWithFrame:self.view.bounds];
        	_WKWebPageView.navigationDelegate = self;
    	}
    	return _WKWebPageView;
	}

WKWebView主要需要实现两个协议WKNavigationDelegate和WKUIDelegate，前一个用于一个页面的加载状态，包括第一次响应成功，加载中，加载完成，加载错误等，后面一个我个人感觉有点意思，主要用于改变UI界面，比如客户端JavaScript使用了一个alert，这个协议可以捕获到，然后使用原生的控件来替代。

WebKit框架还提供了一个任务配置类WKWebViewConfiguration，跟NSURLSessionConfiguration的使用方式类似,可以重新初始化WKWebView的一些配置。

WKNavigationDelegate协议，我主要使用了三个来处理我的应用，加载中，加载完成，加载错误的处理：

	-(void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation
	{
	
	}
	
	-(void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation
	{
	
	}
	
	-(void)webView:(WKWebView *)webView didFailProvisionalNavigation:(WKNavigation *)navigation withError:(NSError *)error
	{
	
	}
	
另外一些属性我用KVO来监听：

	[self.WKWebPageView addObserver:self forKeyPath:@"estimatedProgress" options:NSKeyValueObservingOptionNew context:nil];
    [self.WKWebPageView addObserver:self forKeyPath:@"title" options:NSKeyValueObservingOptionNew context:nil];
    [self.WKWebPageView addObserver:self forKeyPath:@"URL" options:NSKeyValueObservingOptionNew context:nil];
    [self.WKWebPageView addObserver:self forKeyPath:@"canGoBack" options:NSKeyValueObservingOptionNew context:nil];
    
它们分别是，页面的加载进度，页面的标题，页面的URL，页面是否回退过

WKWebView默认是没有开启内置手势的，可以通过allowsBackForwardNavigationGestures来设置

与JavaScript的交互，我目前主要使用[self.WKWebPageView evaluateJavaScript:<#(NSString *)#> completionHandler:<#^(id, NSError *)completionHandler#>]来动态改变页面加载完成之后的css样式，还有WKUserScript *script = [[WKUserScript alloc] initWithSource:<#(NSString *)#> injectionTime:<#(WKUserScriptInjectionTime)#> forMainFrameOnly:<#(BOOL)#>]来创建脚本，它可以明确指定加载前后注入的方式。

**注明：上述的两种创建脚本的方式都是单方向的**

如果我们想互相调用，那么就要实现WKScriptMessageHandler协议了，具体的使用方式可以参考下面给出的的参考资料。

参考资料：

* [WKWebView-NSHipster](http://nshipster.cn/wkwebkit/)
* [WKScriptMessageHandler](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKScriptMessageHandler_Ref/index.html)
* [WKWebView](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKWebView_Ref/index.html)
* [WKWebViewConfiguration](https://developer.apple.com/library/ios/documentation/WebKit/Reference/WKWebViewConfiguration_Ref/index.html)
* [WKUIDelegate](https://developer.apple.com/library/ios/documentation/WebKit/Reference/WKUIDelegate_Ref/index.html)
* [WKNavigationDelegate](https://developer.apple.com/library/ios/documentation/WebKit/Reference/WKNavigationDelegate_Ref/index.html)
