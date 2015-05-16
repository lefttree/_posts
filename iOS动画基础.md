title: iOS动画基础
date: 2015-05-10 01:02:06
tags: iOS
---

貌似在哪个领域里，动画的实现都赋予了软件非常生动的交互，所以我们必须要掌握并学好它。在学习动画之前，首推[Core Animation Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004514)核心动画编程指南，去了解一下官方的术语，定义，以及指导，这很有帮助。

顾名思义，动画也就是让我们的view可以动起来，所谓的view实际上都会继承于UIView，而每一个UIView都有一个CALayer对象，它们两个是分开的，分别用于不同的地方。可以看见的是，UIView用于显示，CALayer用于绘制。（注明：CALayer属于QuartzCore框架）

UIView自身（注明：UIView属于UIKit框架）会有描述位置的信息，比如frame，transform。在使用一些比较简单的动画时，我们应该首选考虑的是使用UIView提供的一些方法，去改变frame，transform等属性。

	[UIView animateWithDuration:1.6 animations:^{
	
	}];

对于高度复杂一些的动画，就需要我们对Core Animation整个的熟悉和了解了。这里，我建议先阅读一下[动画理解](http://objccn.io/issue-12-1/)，把一些概念性的东西了解清楚。比如Core Animation维护的层次结构，什么叫模型层树(model layer tree)，什么叫表现层树(presentation layer tree)，而且你务必要理解，当把动画添加到一个layer的时候，是不直接修改其属性的。

在清楚这些概念性的问题后，你可以去学习一些动画类了，比如CABasicAnimation，CATransform3D，CAKeyframeAnimation,CAAnimationGroup,CAAnimation，另外你务必要记住一点，CAAnimation是这些动画类的鼻祖(它是一个抽象类，不能直接使用)，而且要搞清楚CALayer。

举例一些简单的例子

**使用CABasicAnimation**
	
	CABasicAnimation *animation = [CABasicAnimation animation];
    animation.keyPath = @"position.x";
    animation.duration = 1;
    animation.fromValue = @50;
    animation.toValue = @250;
    [view.layer addAnimation:animation forKey:@"left"];
    
为了验证动画究竟修改了那个地方的值，我打印了一下view的frame，NSLog(@"%@",NSStringFromCGRect(view.frame))，这里可以看的出来，view的frame值没有发生改变，而且在模拟器中，动画结束了，view又回到了原来的位置。说明啊，动画的执行和view的渲染是分开的，如果你还不理解，请继续阅读《核心动画编程指南》或者简单的《动画理解》。

如果我们不想动画结束后view回到原来的位置，可以使用

	animation.fillMode = kCAFillModeForwards;
    animation.removedOnCompletion = NO;

**使用CAKeyframeAnimation**

	CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
    animation.keyPath = @"position.x";
    animation.values = @[@0,@10,@-10,@10,@0];
    animation.keyTimes = @[ @0, @(1 / 6.0), @(3 / 6.0), @(5 / 6.0), @1 ];
    animation.duration = 0.4;
    animation.additive = YES;
    [view.layer addAnimation:animation forKey:@"shake"];
    
**使用CAAnimationGroup**

	CABasicAnimation *right = [CABasicAnimation animation];
    right.keyPath = @"position.x";
    right.duration = 1;
    right.toValue = @250;
    right.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];

    CABasicAnimation *top = [CABasicAnimation animation];
    top.keyPath = @"position.y";
    top.duration = 1;
    top.toValue = @10;
    right.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    
    CAAnimationGroup *animationsGroup = [CAAnimationGroup animation];
    animationsGroup.animations = @[right,top];
    [view.layer addAnimation:animationsGroup forKey:@"group"];

阅读资料：

* [#12动画](http://objccn.io/issue-12/)
* [Core Animation Reference Collection](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/CoreAnimation_framework/index.html#//apple_ref/doc/uid/TP40004513)


