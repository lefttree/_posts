title: 上架AppStore的经历
date: 2015-05-27 12:43:22
tags: iOS
---

##开发之前的准备

关于证书，以及流程，前人已有总结，请阅读

* [iOS App提交指南(一)](http://www.jianshu.com/p/6c75a6e53605)
* [iOS App提交指南(二)-协议、税务和银行业务](http://www.jianshu.com/p/c7cf65911bc1?utm_campaign=maleskine&utm_content=note&utm_medium=mobile_author_hots&utm_source=recommendation)
* [iOS证书说明和发布内购流程整理](http://mp.weixin.qq.com/s?__biz=MjM5OTM0MzIwMQ==&mid=206718165&idx=3&sn=860ec99f81da19cd3d6faffe6d85083b#rd)

##准备开发

* 在开发码农周刊应用之前，先写了一份PRD，把需求写清楚，然后利用storyboard画了一个简单的原型。PRD List我用的是[trello](https://trello.com/)
* 图片资源我统一用Images.xcassets来进行管理
* [pixabay](http://pixabay.com/)搜索一些免费的资源
* [makeappicon](http://makeappicon.com/)生成app的启动icon
* [icons8](https://icons8.com/)下载了一份iOS的icon
* [appscreens](https://appscreens.io/)这个工具用于生成需要提交到AppStore审核的预览图片

##准备提交

在Build Settings Code Signing中设置 Code Signing Identity和Provisioning Profile，用你在apple申请的发布证书和发布描述，然后把Device选择为真机。

![](http://websources.qiniudn.com/iOS/codsigning.png)

选择Product中的Archive构建发布版本，可以直接点击Submit to App Store

![](http://websources.qiniudn.com/iOS/archives.png)

##构建错误与警告

在构建发布版本时Xcode会检测发布App所具备的逻辑，下面是我遇到的错误和警告

error itms -90096 : your binary is not optimized for iphone5 new iPhone apps and app updates submitted must support the 4-inch display on iphone5 and must include a launch image with the -568h size modifier immediately following the<basename> portion of the launch images’ filename. launch images must be png files and located at the top level of your bundle or provided within each. lproj folder if you localize your launch images learn more about iPhone 5 support and app launch images by reviewing the ‘iOS Human interface guidelines at

上传启动动画PNG图片，另外还给出了三个解决方案参考的URL

[参考一](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)[参考二](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/LaunchImages.html#//apple_ref/doc/uid/TP40006556-CH22-SW1)[参考三](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1)

error itms-4238 redundant binary upload. there already exists a binary upload with build version ‘1’ for train ‘1.0’ at software assets/prereleasesoftwareasset

因为想要提交的版本已经存在，更改build版本号

error itms-90032: invalid image path - no image found at the path referenced under key CFBundleIconFiles:’xxx

存在一个不使用的配置，在Info中删除

WARNING ITMS:iTunes Store operation succeeded with a warning
the app references non-public selectors in Payload/manongweekly.app/manongweekly:addContent

查找是否使用了非公用API，一些名字可能会是私有API，最好把名改了

WARNING ITMS 90025:Missing recommended icon file the bundle does not contain an app icon for iPhone/ipodtouch of exactly ‘120x120’pixels in.png format for iOS versions >=7.0

添加76，76@2x的图标PNG

##查看被拒原因与加急

在[Member Center](https://developer.apple.com/membercenter/index.action)中应用审核的最下面，有一个解决方案中心，点击解决方案中心，可以查看被拒原因，审核人员会给你罗列以及解决方案参考。

如果应用审核通过了，在使用的过程中发现有重大的bug导致应用crash，可以申请加急，非常快，[contact](https://developer.apple.com/appstore/contact/?topic=expedite%3E)

最后《码农周刊》iOS客户端愉快的上线了

[![AppStore](https://camo.githubusercontent.com/770175f6c01d89c84a020706126a9e6399ff76c4/68747470733a2f2f696d672e736869656c64732e696f2f636f636f61706f64732f702f4b696e676669736865722e7376673f7374796c653d666c6174)](https://itunes.apple.com/cn/app/yuan-yi-yue/id990227579?l=en&mt=8)  [![manong-reading](https://camo.githubusercontent.com/b0224997019dec4e51d692c722ea9bee2818c837/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f6d6173686170652f6170697374617475732e737667)](https://github.com/lcepy/manong-reading)

[Download on the App Store](https://itunes.apple.com/cn/app/yuan-yi-yue/id990227579?l=en&mt=8) 

[checking on the manong-reading project ](https://github.com/lcepy/manong-reading)
