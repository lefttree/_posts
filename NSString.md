title: NSString
date: 2015-04-18 23:05:37
tags: iOS
---

NSString是我学习的第一个类，它是iOS中专门处理字符串的一个类，包括有从文件读取，从网络读取，标准C的转换，格式化，大小写转换，查询，写入文件等，当然它也有一个对应的Mutable。

**优化指南：尽早开启ARC，以下都是ARC版**

要使用NSString有三种方式，第一种语法糖方式，第二种alloc方式，第三种使用类方法方式。

	NSString *str = @"";
	NSString *str1 = [[NSString alloc] initWithFormat:@"%@",@"hello"];
	NSString *str2 = [NSString stringWithFormat:@"%@",@"hello"];
	
开发中用语法糖或者类方法的方式比较多

###NSString常用

查询前缀，后缀

	NSString *str = @"http://lcepy.github.io";
    BOOL isF = [str hasPrefix:@"http"];
    BOOL isL = [str hasSuffix:@"io"];
    
创建格式化字符串

	NSString *formatter = [NSString stringWithFormat:@"http://%@",@"lcepy.github.io"];
    NSString *formatter1 = [[NSString alloc] initWithFormat:@"http://%@",@"lcepy.github.io"];
    
从网络读取以及响应，当然很少有人会这么干PS :会block主线程

	NSURL *urlS = [NSURL URLWithString:@"http://lcepy.github.io"];
    NSString *urlStr = [NSString stringWithContentsOfURL:urlS encoding:NSUTF8StringEncoding error:nil];
    NSString *urlStr1 = [[NSString alloc] initWithContentsOfURL:urlS encoding:NSUTF8StringEncoding error:nil];
    [urlStr writeToURL:urlS atomically:YES encoding:NSUTF8StringEncoding error:nil];

从本地文件中读取以及写入

	NSString *path = [[NSBundle mainBundle] pathForResource:@"lcepy" ofType:@"html"];
    NSString *fileStr = [NSString stringWithContentsOfFile:path encoding:NSUTF8StringEncoding error:nil];
    NSString *fileStr1 = [[NSString alloc] initWithContentsOfFile:path encoding:NSUTF8StringEncoding error:nil];
    [fileStr writeToFile:str atomically:YES encoding:NSUTF8StringEncoding error:nil];
    
字符串的比较，因为OC是C语言的超集，所以字符串有两种比较方式，指针一样，还有指针一样内容一样的比较，而==一般都不建议使用，它比较的是引用，NSString有它的比较方法。

	isEqualToString
	
	NSString *astring01 = @"This is a String!";
    NSString *astring02 = @"This is a String!";
    BOOL result = [astring01 compare:astring02] == NSOrderedSame;    //NSOrderedSame判断两者内容是否相同
    NSLog(@"result:%d",result);
    BOOL result1 = [astring01 compare:astring02] == NSOrderedAscending;    //NSOrderedAscending判断两对象值的大小(按字母顺序进行比较，astring02大于astring01为真)
    NSLog(@"result:%d",result1);
    BOOL result2 = [astring01 compare:astring02] == NSOrderedDescending;    //NSOrderedDescending判断两对象值的大小(按字母顺序进行比较，astring02小于astring01为真)
    NSLog(@"result:%d",result2);
    
大小写以及首字母大写

	NSString *io = @"hello";
    NSString *IO = @"HELLO";
    NSLog(@"%@",[io uppercaseString]);
    NSLog(@"%@",[IO lowercaseString]);
    NSLog(@"%@",[io capitalizedString]);
    
查询截取PS-->**我觉得NSString使用最频繁的一处，查询截取是在开发中无法避免的事项，它非常有用**其实，在使用查询这一系列的方式时，它和NSRange有紧密相连的关系。NSRange是一个结构体，用来描述起始位置，以及计数。

	NSString *str = @"http://lcepy.github.io";
    NSRange httpRange =  [str rangeOfString:@"http"]; //返回一个range结构体
    NSLog(@"%@",NSStringFromRange(httpRange));
    
    
    NSRange ioRange = [str rangeOfString:@"io"];
    NSString *subs = [str substringWithRange:NSMakeRange(ioRange.location, ioRange.length-1)]; //从指定位置开始截取字符串
    NSLog(@"%@",subs);
    
    //从字符串开头一直截取到指定位置
    NSString *indexStr = [str substringToIndex:4];
    NSLog(@"%@",indexStr);
    
    //从指定位置开始截取到最后的字符串（包括）
    NSString *lastStr = [str substringFromIndex:4];
    NSLog(@"%@",lastStr);

扩展路径以及获取扩展文件名

	NSString *doc = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES)[0];
    NSString *filePath = [doc stringByAppendingPathComponent:@"PhotoCache"];
    NSString *file = @"~/lcepy.io";
    NSLog(@"%@",[file pathExtension]);
	
###NSMutableString常用

注意PS-->**NSString可以使用的方法，Mutable版全部具备，Mutable版的方法，NSString不一定有**

在字符串最后插入或者格式化插入字符串

	NSMutableString *str1 = [NSMutableString stringWithString:@"http://lcepy.github.io"];
    [str1 appendString:@"#login"];
    [str1 appendFormat:@"%@",@"#login"];
    NSLog(@"%@",str1);
    
在指定的位置插入字符串

	[str1 insertString:@"?id=fjdkfjkgfgi384jkg" atIndex:str1.length];
    NSLog(@"%@",str1);
    
在指定的范围内替换

	NSRange login = [str1 rangeOfString:@"#login"];
    [str1 replaceCharactersInRange:login withString:@"#logou"];
    NSLog(@"%@",str1);


延伸阅读：

* [给NSString添加java风格：（感觉有点意思）](http://blog.devtang.com/blog/2012/02/14/nsstring-java-like-wrapper/)
* [NSString](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/index.html)