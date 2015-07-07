title: CALayer基础与寄宿图
date: 2015-07-05 13:26:49
tags: iOS
---

说起来Core Animation是一个复合引擎，它的职责就是尽可能快的组合屏幕上不同的可视内容，这个内容是被分解成独立的图层，存储在一个叫图层树的体系中。如果我们想学习动画，那么一切都从它开始。

##CALayer与UIView的区别

和UIView最大的不同是CALayer不能处理与用户交互的事件，CALayer并不清楚具体的响应链，即使它提供了一些方法来判断是否触摸在图层的范围内。

##CALayer

CALayer与UIView是一个平行的关系，每一个UIView都存在一个layer属性，视图的职责就是创建并管理这个图层，并且确保当子视图在层级关系中被添加或移除时，它们关联在图层在同样的对应的层级关系树中有同样的操作。（不要急，理论虽然有些绕，慢慢理解）

	var layer:CALayer = CALayer()
    layer.frame = CGRectMake(50, 50, 200, 200)
    layer.backgroundColor = UIColor(red: 0.400, green: 0.800, blue: 1.000, alpha: 1.000).CGColor
    self.view.layer.addSublayer(layer)
    
添加一个图层到UIView中，然后运行看看

其实在某种意义上来说，对于一些简单的需求，我们确实没有必要去处理CALayer，那是因为Apple已经通过UIView的高级API间接的使动画变得很简单，使用它何乐而不为呢。

	UIView.animateWithDuration(1.2, animations: { () -> Void in
            
    })

那么，我们使用CALayer到底能实现什么UIView不能实现的呢？

* 阴影，圆角，带颜色的边框
* 3D变换
* 非矩形范围
* 透明遮罩
* 多级非线性动画

##创建寄宿图

所谓的寄宿图也就是在图层中包含的图，有了图片，我们可以展现更丰富的视觉效果。

CALayer有一个属性叫contents，这个属性的类型是id类型，而我们要把图片创建在图层上，就需要使用这个属性，当然在Swift中它是一个AnyObject。

	UIImage *image = [UIImage imageNamed:@"mockingbird.png"];
    self.lodgeLayerView.contents = (__bridge id) image.CGImage;
    
    var image:UIImage = UIImage(named: "mockingbird.png")!
    layer.contents = image.CGImage
    
感觉看起来云豹还算显示正常，万一它有点胖，怎么办呢？如果我们使用UIImageView，那么有contentMode属性可以设置，想一想CALayer是不是也有同样的，遗憾的是，没有，但是它有contentsGravity。

	layer.contentsGravity = kCAGravityResizeAspectFill
	
动动你的手指，每一个试一试，看看图片是怎么现实的。

对于双倍屏幕，其实contentsScale属性目前来说已经使用的好少了，它定义了寄宿图的像素尺寸和视图大小的比例，默认情况下它是一个1.0的浮点数。

在某些情况下，我们的云豹已经显示正常大小了，但是你可能发现它超出边界了，那么，亲你能用上maskToBounds属性了，设置为true，它就在边界内了。

	layer.masksToBounds = true
	
近些年来在用css做网站时，大家都非常流行使用图片精灵的方式去处理图片，为什么？因为它能优化性能。那么在iOS开发中一样可以，我们要用的就是contentsRect属性。

这个坐标系统相对有些绕，因为它采用的是单位坐标，它的值只在0-1之间，用来描述相对位置。

	layer.contentsRect = CGRectMake(0.5, 0, 0.5, 0.5)
	
如果我们想使寄宿图可以均匀的拉伸，那么contentsCenter属性是最佳选择，不过要记住一点，它不会主动去拉伸，除非是图层变了。

	layer.contentsCenter = CGRectMake(0.25, 0.25, 0.5, 0.5)
	
##自定义图层

	override func drawLayer(layer: CALayer!, inContext ctx: CGContext!) {
        
    }
    
Apple的建议是如果你没有这方面的需求，最好不要写一个空的drawLayer方法在你的代码中，因为一旦视图检测到被调用，那么就会分配一个图层。

在这里，我们主要是利用Core Graphics去绘制一些寄宿图，然后添加到上下文中。
	
