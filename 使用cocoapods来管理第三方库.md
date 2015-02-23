title: 使用cocoapods来管理第三方库
date: 2014-12-31 13:52:58
tags: iOS
---

* [http://cocoapods.org/](http://cocoapods.org/)
* [CocoaPods安装和使用教程](http://code4app.com/article/cocoapods-install-usage)
* [在 Swift 中使用 CocoaPods](http://blog.jobbole.com/72107/)
* [用CocoaPods做iOS程序的依赖管理](http://blog.devtang.com/blog/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/)
* [https://github.com/CocoaPods/CocoaPods](https://github.com/CocoaPods/CocoaPods)

**关于配置，可以参考贴出来的上述的文章，唐巧的博客也写的比较详细**

在需要cocoapods来做管理的项目中建立Podfile文件，先通过pod search 来搜索所需要的库是否支持cocoapods，然后pod:(库名)，pod install来安装。

##坑处收集（针对xcode 6.1 实际项目出现的问题）

* 安装时可以把ruby的镜像替换成淘宝的，第一次pod setup时稍微有些慢，需要耐心等待。
* 安装库时，如果出现链接阶段的错误，首先要查找一下link search paths 路径的问题，还有libpods.a文件是否添加到了library中。
* 安装库时，（最好能开代理就开代理，有时github网络会导致编译或者链接阶段出现莫名其妙的问题）因为文件没有下载全。
* 安装之前，最好是先command + shift + k clean一下编译的缓存，安装之后如果出现问题，也可以先clean一下，再编译尝试。
* 安装之前，最好先把项目关闭了，pod install 后，会在项目的根目录，生成一个新的xcworkspace文件，以后打开项目使用它，如果还用老的，打开又会出现莫名其妙的问题。
* 如果上述都没法解决问题，全部删除，重新来过。