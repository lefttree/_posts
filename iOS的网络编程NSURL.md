title: iOS的网络编程NSURL
date: 2015-04-22 12:37:14
tags: iOS
---

如果只是说iOS中的URL系统，它的东西还不算比较多。但是，如果想用好它，可能还需要具备一些其他方面的知识，特别是多任务编程，比如GCD，NSOperation，NSOperationQueue，NSURLSession等等，而且苹果公司在后期还推了一个叫NSURLComponents的东西，这也是需要去学习的。

一个简单的URL是这样发送出去的：

	NSString *path = @"http://lcepy.github.io";
    NSURL *url = [NSURL URLWithString:path];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    NSURLResponse *response = nil;
    NSError *error = nil;
    [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:&error];
    
基本上如果要构造一个request需要先构造一个NSURL对象，然后通过NSURLConnection发送出去。NSURLRequest是一个不可变的对象，默认发送的是GET的请求，如果想要发送POST请求，可以用它可变的NSMutableURLRequest来支持POST请求，包括超时时间等等。

关于NSURLConnection有三种方式可以发送请求，前面同步版本我们已经看过了，下面是异步和使用协议的方式：

	[NSURLConnection sendAsynchronousRequest:request queue:nil completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
        
    }];
    
协议：

	//实现NSURLConnectionDataDelegate协议
	
	NSURLConnection *connection = [NSURLConnection connectionWithRequest:request delegate:self];
    [connection start];
    
    //常用的需要实现下面四种方法
    
    -(void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error
	{
    	//请求超时，错误的时候
	}

	-(void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response
	{
    	//服务器第一次响应一个response的时候
	}

	-(void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
	{
    	//传输数据
	}

	-(void)connectionDidFinishLoading:(NSURLConnection *)connection
	{
    	//完成的时候
	}
	
对于某些任务，比如下载图片，文件等等大数据的时候，可以使用一个临时会话来启动一个请求，也就是构造一个NSURLSession。

	NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSURLSession *session = [NSURLSession sessionWithConfiguration:configuration];
    NSURLSessionDataTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
        
        //请求的结果
    }];
    [task resume];
    
临时会话的好处是每一个都不是在主线程执行，写的代码量比较少，适合简单的任务。

**参考资料**

* [NSURL/NSURLComponents](http://nshipster.cn/nsurl/)
* [NSURLRequest](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURLRequest_Class/)
* [NSURL](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURL_Class/index.html)
* [NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html)
* [NSURLSession](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSURLSession_class/)
* [NSURLResponse](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURLResponse_Class/index.html)
* [从 NSURLConnection 到 NSURLSession](http://objccn.io/issue-5-4/)
* [NSURL详解](http://ubluesky.com/archives/55)