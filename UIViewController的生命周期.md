title: UIViewController的生命周期
date: 2015-02-16 21:54:06
tags: iOS
---
关于生命周期的问题，主要是在某些情况上必须要使用它，以及一些一系列的管理。比如在视图准备呈现在屏幕之前，我可能要做某些事情。在视图准备消失在屏幕之前，需要做某些事情。当内存不足的时候，需要做某些事情等等。而UIViewController的生命周期，就是来负责这些问题的处理。

**呈现视图的顺序**

它内部的顺序是这样的，先走init方法，如果有自定义视图，则走自定义视图。如果没有，则走xib或storyborad，如果这个也没有则走loadView方法，loadView方法系统调用，也可以重载它。当我们重载它时，可以[super loadView] 父类帮助我们创建一个空的view，如果不调用父类，那么就要自己创建一个UIView，并且self.view = UIView。

如果在调用loadView时不调用父类，也不创建，如果使用self.view的getter method并且view等于nil，会发生自我循环调用。一般情况下，不要在初始化时做view相当的动作。在viewDiload中调用一些网络访问的动作，可以在初始化方法中做一些关于模型数据的准备工作。

如果覆盖了loadView，则必须创建UIViewController的view属性。如果没有覆盖该方法，UIViewController会默认调用initWithNibName方法来初始化并加载view。

**生命周期**

一般来说我们需要知道应用程序什么时候在屏幕上，什么时候从屏幕上消失，这个就是生命周期了。初始化的方法可以在viewDidLoad，这个方法在生命周期中只会调用一次，当然不要忘记了调用它的父类的viewDidLoad方法，[super viewDidLoad]。在调用这个方法时，实际上视图并没有在屏幕上确定下来，于是一般来说，如果视图的几何操作，不要放在这个方法中执行。

viewWillAppear，这个方法会被多次调用，一般来说一次性的初始化不要放在这个方法中实现，但是如果是基于模型的动态初始化，可以放置在这个方法中实现。这个方法会在视图即将要在屏幕上呈现之前调用。

当视图从屏幕上消失时，也会得到通知，viewWillDisappear方法。这个方法是在视图将要从屏幕消失之前调用。

viewDidAppear视图已经呈现在屏幕上调用 viewDidDisappear视图已经从屏幕上消失调用。

viewWillLayoutSubviews  viewDidLayoutSubviews 这两个方法可以在，比如从竖屏到横屏，系统会自动元素的位置，第一个方法是在移动之前调用，第二个方法是在移动完成之后调用，需要设置Autorotation属性。

didReceiveMemoryWarning 当内存不足时，系统会调用这个方法，发送警告。 

##顺序

viewDidLoad -> viewWillAppear -> viewDidAppear -> viewWillDisappear -> viewDidDisappear

关于layout的顺序

viewWillLayoutSubviews -> viewDidLayoutSubviews
