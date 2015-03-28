title: 纪录开发第一个iOS客户端遇见的坑
date: 2015-03-28 14:51:34
tags: iOS
---

来到新公司做的第一个产品就是iOS客户端，我负责的是基于LeanCloud SDK开发的售后服务IM系统，也就是一个聊天的模块。第一次比较纯粹的在工作中，写Objective-c，遇到了一些坑，解决之后才发现原来是这么回事，而且还复习了它的基础语法知识。

**block的问题**

引用资料：

* [谈Objective-C Block的实现](http://blog.devtang.com/blog/2013/07/28/a-look-inside-blocks/)
* [Block防止循环引用](http://www.cnbluebox.com/?p=255)



本来我想使用Block来解决通信的问题，后来我发现语法以及对Block的理解有些忘记了。基础不扎实，带来了严重的后果。Block使用^来声明，用;来结束一个完整的Block，缺一不可。

	void (^ blockde)(NSString *,NSString *) = ^(NSString *test1,NSString *test2){
        NSString *log = [NSString stringWithFormat:@"%@,%@",test1,test2];
        NSLog(@"%@",log);
    };
    
Block的语法有些难度，特别是灵活运用的时候，但是记住一点，先声明返回值，然后两个括号用来描述这个Block的签名，以及参数类型，如果是OC对象，需要写上指针符号。如果没有签名，可以什么也不写。**有一个情况例外，利用typedef定义的block必须写上签名**

我想在动态方法中提供一个Block来处理一些事情，定义它有两种写法。

第一种写法是利用typedef定义一个别名，描述还是第一项声明返回值参数，第二项别名签名，第三项传入的参数类型。

	typedef NSString *(^handlerBlock)(NSDictionary * dic,NSArray * arr);

	-(void)sendMessageText:(handlerBlock)block
	{
		block();
	}
	
第二种写法是直接写在动态方法中，方法的参数形式一般都是()名字，所以block的声明就要写在一个圆括号中，这种写法可以省略block的签名。

	-(void)setSource:(NSString *) source andBlock:(void(^)(NSString *treme)) block
	{
    	block();
	}

因为公司的项目都是ARC的，所以copy就省略了，其他方面主要是需要避免循环引用的问题，比如使用__weak关键字给self对象。


**导入第三方库的问题**

这个IM模块是基于第三方SDK实现的，所以需要导入第三方库，在这里也踩了下坑。LeanCloud依赖了其他五个框架，需要手动的添加到Link Binary With Libraries中，必须得承认学艺不精的后果是这个编译的错，琢磨了很久，而且还需要把openssl的include的路径添加到Head中。

关于Xcode的使用，一定要把cocoaChina的那个Xcode系列阅读完。

**开发者证书的问题**

根据以往的经验想真机调试，那就必须的添加开发者证书，于是按照老旧的流程，去https://developer.apple.com/account/ios/certificate/certificateList.action 下载证书，然后添加设备UID，接着添加Profile一路Next。

现在这些步骤真的不需要了，在Xcode中登录自己的Apple ID，然后在项目的Team选项中选择正确的签名，Xcode自动下载，自动匹配设备UID，一切自动化，当然首先要把设备连接好。

它不是坑，而是因为自己不知道，在安装证书时发现了现在有这么方便的流程。	

**访问相册或者摄像头返回给web端**	

引用资料：

* [GCD](http://blog.devtang.com/blog/2012/02/22/use-gcd/)
* [NSFileManager](http://nshipster.cn/nsfilemanager/)

在拿到图片image对象后，怎么处理，是一个非常头疼的问题。LeanCloud的AVIMImageMessage类需要的是一个filePath而不是UIImage或者NSData对象，iOS应用是没有权限访问应用沙盒之外的东西的。首先想到的解决办法是把这个UIImage写入到应用内的Documents目录下的Photos目录，然后把这个地址发送给AVIMImageMessage对象。考虑到文件IO操作，还会对应用界面卡顿，所以把这个任务委托给了GCD去调度。

在使用dispatch_async之前，还需要处理一个问题，那就是UI展示的问题。大坑在这里，我们的UI是web的。所以对于图片，调用UIImageJPEGRepresentation去压缩体积，然后再转成base64，把这个base64发送给客户端。

为什么说这里是坑呢？因为它是一个IM系统，操作会非常的频繁，最关键的是展示是在web中，它的交互流程很坑。

**使用ionic添加插件时的巨坑**

在升级了ionic为1.3.18之后，它的plugin编译时需要手动添加，据说这个版本他们去掉了自动添加的模块，于是写在配置文件中的插件，需要在platform add ios后，再ionic plugin add im手动添加到编译环境中，每一个都需要重复上述的动作，最后ionic build ios。

希望下一个版本，ionic能把这个补上吧。

