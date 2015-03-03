title: 有趣味的iOS动画实现
date: 2015-03-03 09:40:21
tags: iOS
---

在做iOS应用时，真的为它提供的动画框架感到折服，理解简单，而且可以有多种选择，既可以从底层开始描绘动画，也可以使用高级API，设置apple还提供了模拟物理世界的动画与交互系统，强大牛逼的一塌糊涂。

想要学习这些动画，需要具备一个点，理解iOS中的坐标系统，也就是几个属性。

	dropview.frame;
    dropview.center;
    dropview.bounds;
    dropview.transform

动画，是顾名思义的，在平面或者多维度的移动，frame中记录了当前视图相对于父视图左上角顶点的左边，而center则是中心原点，transform则代表了比如旋转等等矩阵的改变属性。

最简单的动画，就是操作这些属性，改变其值，使用的是UIView的类方法，比如：

	[UIView animateWithDuration:1.0 animations:^{
	      
	} completion:^(BOOL finished) {
      
	}]

其实我最近研究了一下UIDynamicAnimator，它提供了一个模拟真实物理世界的动画与交互系统，比如可以定义一些行为，重力行为，碰撞行为等等。

	-(UIDynamicAnimator *)animator
	{
    	if (!_animator) {
        	_animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.gameview];
    	}
    	return _animator;
	}
UIDynamicAnimator需要一个通用的视图，而动画则是在这个视图上发生，所以init的时候添加了一个通用视图上去。然后可以去实现其他的物理类，比如重力，把它add到UIDynamicAnimator中。

	-(UIGravityBehavior *)gravit
	{
    	if (!_gravit) {
        	_gravit = [[UIGravityBehavior alloc] init];
        	_gravit.magnitude = 0.9;
        	[self.animator addBehavior:_gravit];
    	}
    	return _gravit;
	}

最后一步是要把需要被实现物理动画的视图，添加到重力系统中去。

	[self.gravit addItem:dropview];
	
从技术实现的角度上来说，iOS平台实现这些动画，包括绘制，物理交互，都非常的简单，主要是要注意一些细节的处理，比如当前动画停止之后处理什么，在动画执行过程中，可能会出现一些情况，然后处理，或者是几个动画的连接延迟等等。