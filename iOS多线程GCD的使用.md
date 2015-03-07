title: iOS多线程GCD的使用
date: 2015-03-07 17:07:36
tags: iOS
---
在做iOS开发的时候线程的使用场景还是蛮多的，比如网络请求，比如大的数据处理等等。因为iOS只有一个主线程，而且这个主线程是让UIKit来使用的，如果我们不想让UI界面因为某些操作，那么就必须把一些需要时间等待的操作，分配给其他的线程去处理，并且保证主线程不让UIKit之外的程序来使用。

在UIKit中某些对象是可以在其他线程中正常工作的，但是UIKit的大部分是只能在主线程中正常工作的，明白这一点非常的重要。

iOS中实现多任务编程，主要使用三种方式，1、NSThread 2、NSOperation 3、GCD；个人感觉GCD比较好使用，它主要是与block来配合使用的。比如在网络活动时，我想在[http://wow.163.com](http://wow.163.com)下载一张图片显示在UIImageView，但是下载的过程，如果不使用其他线程来下载图片数据，UI界面会有卡顿的感觉，所以这里就会使用多线程来处理了。

GCD主要可以使用的函数有几个：

* dispatch_queue_create(“name”,NULL) 
* dispatch_async(dispatch_queue_t queue,^{})
* dispatch_get_main_queue
* dispatch_sync
* dispatch_get_global_queue

dispatch_get_main_queue顾名思义，运行在主线程的Main queue，可以通过dispatch_get_main_queue获取。

dispatch_async用于异步操作

dispatch_sync用于同步操作

dispatch_queue_create一般用来创建串行queue，而且只可能创建一个线程，顺序执行

dispatch_get_global_queue 添加到主queue的可能会并行执行，也就意味着创建了多个线程

回到例子上来说，如果我有两个界面，从其中一个界面点击一个按钮到另外一个界面中显示一张图片，而这个图片需要在网络上下载。如果这里不利于多线程，在视觉上得到的反馈，就有可能在第一个界面中卡顿了，因为网络下载把主线程阻塞了。

例子：

		//创建一个请求
        NSURLRequest *request = [NSURLRequest requestWithURL:self.imageURL];
        NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration ephemeralSessionConfiguration];
        NSURLSession *session = [NSURLSession sessionWithConfiguration:configuration];
        NSURLSessionDownloadTask *task = [session downloadTaskWithRequest:request completionHandler:^(NSURL *location, NSURLResponse *response, NSError *error) {
            if (!error) {
                if ([request.URL isEqual:self.imageURL]) {
                    UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:location]];
                    dispatch_async(dispatch_get_main_queue(), ^{
                        self.image = image;
                    });
                }
            }
        }];
        [task resume];	

在这个例子中主要是利用了NSURLSession临时会话，NSURLSessionDownloadTask任务，NSURLSessionConfiguration配置与dispatch_async相结合的方式来处理的多线程。




