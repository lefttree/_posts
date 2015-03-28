title: UINavigationController的理解
date: 2015-02-01 19:23:41
tags: iOS
---

在做iOS开发的时候，**UINavigationController**可以说使用跟那个UITableView一样非常的常见。在学习这个之前，需要了解一下在iOS开发中的层级关系。

我把它想象成一本书，虽然这么描述非常的不精准，但是却很像。每一个UIViewController就像书页，最外面的一个是封页，当然它就是我们的启动UI界面了，而目录就相当于是UINavigationController，通过它可以精准的翻到每一页。在iOS应用中，这样的层级关系就像书页一页。

> 实际上这里可能描述的有些错误，但是我只能这么想象了，当我翻到某一页时，这一页（UIViewController）就是在顶端的，而其他页（UIViewController）是在下面的。当然，对于导航控制，它想要控制每一页（对于书来说是人在控制）就必需在每一个视图控制器的根部。

UINavigationController是一个栈结构，所谓的栈结构也就是一个顺序（先进后出的顺序），因为当前的书页我们要看见，它永远都必须在第一的位置，我们才能看见，也就决定了它是我们人看完之后才能出去的顺序。

如何创建一个UINavigationController？

 	ViewController *view = [[ViewController alloc] init];
    UINavigationController *navigation = [[UINavigationController alloc]
    initWithRootViewController:view];
    self.window.rootViewController = navigation;

这里会要求加入一个根视图控制器，也就是初始化时我们看见的那一页。

从官方的.h文件中可以看的出来用来进行导航的就四个方法，协议的不算。

	- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated; // Uses a horizontal slide transition. Has no effect if the view controller is already in the stack.

	- (UIViewController *)popViewControllerAnimated:(BOOL)animated; // Returns the popped controller.
	- (NSArray *)popToViewController:(UIViewController *)viewController animated:(BOOL)animated; // Pops view controllers until the one specified is on top. Returns the popped controllers.
	- (NSArray *)popToRootViewControllerAnimated:(BOOL)animated; // Pops until there's only a single view controller left on the stack. Returns the popped controllers.

分别是添加到视图控制器入栈，移出最后一个视图控制器，移出到跟视图控制器上，移出到任意位置上的视图控制器。

除了上述的方法外，还有几个属性需要掌握，在写到这个之前，还需要理解导航控制上的一些概念问题。对于管理多个内容视图的push和pop，导航控制器自身提供了视图切换的动画效果。一般来说一个app中的内容区域就相当于视图控制器，而这个框就是导航控制器，当然这个框又是在另一个框上的，比如window。假设一下，当我们切换了内容后，有些app是上部的一个工具条不会变中间的内容变，也有可能中间和上部都会变。从这里，其实可以看的出来，导航控制存在着这些toolBar，也控制着视图控制器，但是这些toolBar的真正控制权是在每一个视图控制器上的。

总结起来

> 一个navigationController对应一个navigationBar，一个navigationController包含多个UIViewController，每个UIViewController对应一个UINavigationItem，UINavigationItem控制着多个UIBarButtonItem，一个UINavigationController控制一个UIToolBar，UIToolBar中的UIBarButtonItem由当前视图控制器管理，而不是导航控制器来控制。需要注意的是，一个UINavigationItem不是由navigationbar或者是导航控制器来控制，而是由当前视图控制器来控制。


对于生成一个系统自带的UIBarButtonItem，从代码上就可以看的出来它不是self.navigationController在控制，当然有系统的一般来说肯定有自定义的。

	UIBarButtonItem *leftbutton = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemBookmarks target:self action:@selector(leftEvent)];
    self.navigationItem.leftBarButtonItem = leftbutton;

	UIButton *button1 = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    [button1 setTitle:@"view" forState:UIControlStateNormal];
    [button1 setTitleColor:[UIColor blackColor] forState:UIControlStateNormal];
    [button1 setFrame:CGRectMake(0, 0, 40, 40)];
    [button1 addTarget:self action:@selector(customviews:) forControlEvents:UIControlEventTouchUpInside];
    UIBarButtonItem *right = [[UIBarButtonItem alloc] initWithCustomView:button1];
    
    self.navigationItem.rightBarButtonItem = right;
    
    
自定义一个button放到navigationItem的右边的bar中

	UIButton *button = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    [button setTitle:@"Push" forState:UIControlStateNormal];
    [button setTitleColor:[UIColor blackColor] forState:UIControlStateNormal];
    [button setFrame:CGRectMake(90, 100, 140, 40)];
    [button addTarget:self action:@selector(pushview:) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:button];
    
    -(void)pushview:(UIButton *)sender
	{
    	SecondViewController *secoundVC = [[SecondViewController alloc] init];
    	[self.navigationController pushViewController:secoundVC animated:YES];
	}

然后创建一个按钮，导航到第二个视图控制器上。

[源代码查看](https://github.com/lcepy/LearnObjective-C/tree/master/NavigationControllerDemo)
