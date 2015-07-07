title: CALayer几何
date: 2015-07-07 21:09:43
tags: iOS
---

了解iOS的几何学，是非常有帮助的，从而可以认识到它是如何控制位置，尺寸以及对自动布局的影响。

UIView和CALayer有三个非常重要的布局属性，frame,bounds和center与position，实际上center和position是一个相同的值，只是为了区分UIView和CALayer。

frame是视图相对于父视图的坐标系，而bounds则是内部坐标，一般情况下{0,0}在内部坐标中描述的是左上角。center和position都代表了相对父视图的anchorPoint所在的位置，anchorPoint的坐标一般情况下都是图层的中心点。

anchorPoint的坐标相对于是你有一张白纸，有一个图钉，在这个白纸的范围内你的图钉一般可以定在中间，左右上下角，而这几个点都可以成为anchorPoint的坐标。

这几个属性是互相影响的，如果你的center或者position坐标变了，也会相应的改变frame和bounds，当然图层还是在原来的高宽组成的矩阵范围之内。

	self.geometryView = [[UIView alloc] initWithFrame:CGRectMake(10, 74, 200, 200)];
    self.geometryView.backgroundColor = [UIColor grayColor];
    [self.view addSubview:self.geometryView];
    
    self.layer = [CALayer layer];
    self.layer.frame = CGRectMake(50, 50, 100, 100);
    self.layer.backgroundColor = [UIColor colorWithRed:0.000 green:0.502 blue:0.502 alpha:1.000].CGColor;
    self.layer.borderWidth = 1;
    self.layer.anchorPoint = CGPointMake(1, 1);
    self.layer.borderColor = [UIColor blackColor].CGColor;
    [self.geometryView.layer addSublayer:self.layer];

运行这段代码看一看

和视图一样，图层在图层树当中也是相对于父图层的层级关系放置的，如果父图层进行了移动，那么图层也会根据父图层发生移动，也就是意味着position属性可能会发生改变。

这样对于放置图层会更加方便，因为你可以通过移动根图层来将它的子图层作为一个整体来移动，但是有时候你需要知道一个图层的绝对位置，或者是相对于另一个图层的位置，而不是它当前父图层的位置。

CALayer给不同的坐标之间图层提供了一些工具方法，可以进行判断。

	- (CGPoint)convertPoint:(CGPoint)point fromLayer:(CALayer *)layer; 
	- (CGPoint)convertPoint:(CGPoint)point toLayer:(CALayer *)layer; 
	- (CGRect)convertRect:(CGRect)rect fromLayer:(CALayer *)layer;
	- (CGRect)convertRect:(CGRect)rect toLayer:(CALayer *)layer;

虽然CALayer并不关心任何响应链事件，但是它也提供了一些方法来帮助我们处理事件

	-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
	{
    	CGPoint point = [[touches anyObject] locationInView:self.view];
    	point = [self.geometryView.layer convertPoint:point fromLayer:self.view.layer];
    	if ([self.geometryView.layer containsPoint:point]) {
        	point = [self.layer convertPoint:point fromLayer:self.geometryView.layer];
        	if ([self.layer containsPoint:point]) {
            	[[[UIAlertView alloc] initWithTitle:@"Inside layer"
                                        message:nil
                                       delegate:nil
                              cancelButtonTitle:@"OK"
                              otherButtonTitles:nil] show];
        	} else {
            	[[[UIAlertView alloc] initWithTitle:@"Inside view"
                                        message:nil
                                       delegate:nil
                              cancelButtonTitle:@"OK"
                              otherButtonTitles:nil] show];
        	}
    	}
	}
	
touches系列的方法，可以通过point来探知究竟触摸在图层的哪一个范围内。

相对于这些，图层还有一个三维空间，比如两个图层，谁在上面（可视）谁在下面，zPosition就是来干这个事情的，不过一般很少会使用，你这么设计会比较反人类。想象一下，如果我是一个画家，我在画板上绘画，一般都是后面绘画的画会覆盖在前面绘画的画上，虽然这个属性可以用并且调整，但是我们还是要遵循一下传统，尽量别这么设计。

自从iOS6以来，Apple推出了约束布局，虽然操作视图比如UIView中，可以利用它暴露出来的NSLayoutConstraintAPI，但是CALayer有点麻烦，需要人工，最简单的办法是使用CALayerDelegate。

	- (void)layoutSublayersOfLayer:(CALayer *)layer;
	
来进行重新计算坐标系。



	

