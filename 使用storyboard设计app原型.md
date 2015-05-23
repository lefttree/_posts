title: 使用storyboard设计app原型
date: 2015-05-23 20:48:13
tags: iOS
---

今天我想写一下自己利用storyboard+swift快速设计一款App原型的经历，这是突然发现Xcode的强大之处带来的优势，你可以设计一个如下图的原型。

![](http://websources.qiniudn.com/iOS/prototype.png)

先创建一个项目＋创建一个storyboard＋拖拽一个ViewController到storyboard中（默认你知道怎么创建一个项目，怎么新建一个storyboard，以及怎么拖拽一个ViewController到storyboard中）

![](http://websources.qiniudn.com/iOS/storyboard.png)

给ViewController设置启动，这个设置针对storyboard是唯一的，也就是只能设置一个

![](http://websources.qiniudn.com/iOS/initViewController.png)

给ViewController设置身份ID(identity)，它应该是唯一的，而且后期如果有多个storyboard时场景的跳转会使用到

	var sd:UIStoryboard = UIStoryboard(name: "Cycling", bundle: nil)
    var cyc:CyclingInitViewController = sd.instantiateViewControllerWithIdentifier("CyclingInit") as! CyclingInitViewController
    self.navigationController?.pushViewController(cyc, animated: true)

![](http://websources.qiniudn.com/iOS/identity.png)

给ViewController应用一个NavgationController，每一个视图控制器中都有一个navigationController和navigationItem。（注意：前者应该算一个公共的，后者navigationItem是每个控制器自身独有的）在使用的时候需要注意这些，以保证标题，以及导航栏上的bar可以设置正确

![](http://websources.qiniudn.com/iOS/navigationC.png)

拖拽一个label到视图中，我们可以通过属性板来设置它的一些属性，比如字体大小，颜色，是否居中。属性有很多，基本上和代码中的UILabel可以一一对应起来。
 
![](http://websources.qiniudn.com/iOS/attributes.png)

在Size面板中可以调整x,y以及高宽

![](http://websources.qiniudn.com/iOS/size.png)

接下来应该是一个最重要的活儿了，autolayout技术对视图进行约束，这是接下来想对UI进行正确排版所使用的技术，关于它的细节问题，你需要慢慢了解，我只能告诉你怎样在storyboard中利用Xcode给我们提供的工具去连线，去进行约束。

第一种方式，利用右下角的小三角形，对约束进行重置（简单的约束，Xcode非常智能的帮你排版了）

![](http://websources.qiniudn.com/iOS/constraintsimage.png)

第二种方式，利用连线去设置constraints，属性比较多，可以记一下，上下左右高宽都有，还有居中什么的

![](http://websources.qiniudn.com/iOS/constraints3.png)

第三种方式，利用代码去设置constraints（一般来说，我们是在设计原型，如果用代码...感觉杀鸡焉用牛刀了）推荐一个库[Masonry](https://github.com/SnapKit/Masonry)（注明：现在还没有swift版的）

最后大家看看我们设计的一个结果吧，源代码可以在Github上查看[storyboard-app-prototype](https://github.com/lcepy/storyboard-app-prototype)

![](http://websources.qiniudn.com/iOS/result.png)