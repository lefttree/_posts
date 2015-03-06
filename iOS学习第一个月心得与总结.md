title: iOS学习第一个月心得与总结
date: 2015-01-02 23:45:14
tags: iOS
---

从买来Mac之后，还是看的池建强老师的一篇文章《程序员如何选择技术方向》，就开始自发的学习iOS了，因为我也想做一个自己的应用，想学一门编译型的语言，说来也巧，买Mac电脑，还是看了池老师《Mac Talk人生元编程》才开始真正下定决心入手了一台。

在开始学习iOS编程之前，我先重新去复习了一下C语言，这个是非常有必要的。虽然现在Swift已经出来了，但是Objective-C还是需要掌握的，而它又是C的超集，无可厚非，于是从头开始。

C语言总结：

* 学习它的基本类型，比如int float等
* 学习数组，了解C语言数组是没有边界的，以及数组的指针是如何指向的
* 学习C语言的函数，了解它的一些传参，返回值等等，C语言中的函数如果想改变参数的值，那么必须传入指针类型
* 学习内存分配，以及如何分析问题
* 学习指针*，地址符&
* 学习结构体，静态类型
* 学习宏定义，文件包含，条件编译
* 学习typedef
* 学习指向一维，二维数组的指针，指针字符串，以及指向函数的指针与返回指针的函数
* 枚举
* 个别C语言函数库

总体来说学习完C语言之后，再学习OC非常的轻松，很多东西都可以command + 鼠标左键 去h文件中自己看，苹果的方法长的跟句子一样，真的很难记住，所以必须要看头文件了。而且学习完C语言，对于内存的分配，更加的清楚了。

Objective-C语言语法总结：

* 学习OC后的结果第一条就是要学习如何看源代码
* @interface定义一个类，在h文件中，以及如何在m文件中实现这个类@implementation，它们都有@end来结尾
* 了解OC是单继承，所以一个类只能继承一个父类，但可以有多个子类，OC中的继承是在头文件定义的类名后面:UIViewController 写上需要继承父类的类名
* OC里也有成员变量，可以用一个大括号括起来，它也有如@public @private等，如果没有显示的声明，那么默认都是@protected
* 接着是学习如何定义方法，- + 以及self的作用，－表示实例方法，+表示静态方法，self表示当前谁用就指向谁
* 学习如何传入参数-(int)number:(int)num; 第一个括号表示返回值，冒号也算方法名，后面跟着的括号，表示传入一个int类型的参数
* 在成员变量中，可以显示的声明getter，setter方法，而在OC中这两种可以使用点语法来使用
* 学习分类category，在xcode中创建的文件一般都是类＋分类名比如@interface Game:(Test)，通过它可以实现对类的扩展
* 学习协议protocol，这玩意在做实际项目时会大量的运用到，它跟接口非常的类似，OC不强制要求实现全部的协议，使用的时候在类名后面加上尖括号<xxxDelegate>要求实现这个协议
* block语法，^{}，这玩意跟Js中的callback函数非常的类似，只是OC实现的语法会非常的奇怪int(^number)(int,int)第一个int表示返回值，中间括号表示block的名字，最后一个括号表示传入参数的类型，这玩意字符多的时候看起来会有些怪异和费解
* 一些循环，for for in 或者block
* 内存管理，OC是使用引用计数的方式来管理内存的，所以创建就必须对应有释放，现在因为已经有了ARC所以这部分代码可以不用写了，编译器会帮助我们自动补全。alloc retain copy ＋1 relese -1 0的时候系统释放，一般情况下，谁使用，谁释放的原则，比如我现在用alloc创建一个对象，那么我就需要在relese它，如果我把这个对象setter给另一个对象当属性，那么这个对象就要retain一次，在dealloc这个方法中对它relese，原则就是这样的，谁使用，谁释放。
* @property属性，OC提供了一个快速创建属性并默认实现getter setter方法的东西，主要是配合原子性，retain copy来使用，在开启ARC的环境下，可以用weak strong来使用，这个主要对应UI方面，一般情况下只是赋值用assign，NSString用copy，其他OC对象用retain
* 学习copy语法，了解什么是浅拷贝，深拷贝

<!--more-->

接下来是学习Foundation框架提供的OC类型了，都可以用@来快速实现，比如@""字符串，@[]数组，@{}字典，唯一的缺点是这些创建的是不可变的，如果要创建可变的，就要使用NSMutable ...之类的了，前缀名字都一样。每一个类型都有N多方法，而且名字，长的根句子一样，所以就不写了，比如字符串，有创建，格式化，截取，数组，分割，排序等都有，需要学习Foundation提供的这些方法，因为它跟后面编写应用时，处理数据息息相关。

UIKit学习，到这里就开始进入应用编程的范围了，学习它是关于如何搭建UI界面，比如，如何使用xib，storyboard，用代码实现，认识UIViewController，以及如何监听事件，实现各控件的代理等。

UIKit学习总结：

* 初识MVC

官方的推荐任何一种对象都分配成三种角色。控制器可以直接访问模型和视图，但是模型跟视图是不允许互相访问。模型不允许直接访问控制器，从设计角度上来看，模型应该是重用的，如果下一个项目或者别的需要使用这个模型，如果模型中包含了控制器，那么就不可以使用了。模型应该关注的自身的数据，究竟这个数据需要放在哪里，则由控制器来控制。在控制中设置数据源datasource，当视图对控制器发送一个消息询问，数据源是啥，来索要数据。而它本身是不知道数据是哪个，而是由控制器来告知。

* 清楚UIViewController的生命周期

先走init方法，如果有自定义视图，则走自定义视图。如果没有，则走xib或storyborad，如果这个也没有则走loadView方法，loadView方法系统调用，也可以重载它。当我们重载它时，可以[super loadView] 父类帮助我们创建一个空的view，如果不调用父类，那么就要自己创建一个UIView，并且self.view = UIView。

如果在调用loadView时不调用父类，也不创建，如果使用self.view的getter method并且view等于nil，会发生自我循环调用。一般情况下，不要在初始化时做view相当的动作。在viewDiload中调用一些网络访问的动作，可以在初始化方法中做一些关于模型数据的准备工作。

如果覆盖了loadView，则必须创建UIViewController的view属性。如果没有覆盖该方法，UIViewController会默认调用initWithNibName方法来初始化并加载view。

viewWillAppear -> viewDidAppear（控制视图显示）viewWillDisappear -> viewDidDisappear （控制视图卸载）

* 学习基础UI控件，比如UILabel UIButton等，创建基本上各控件都是一样的，可以用构造方法，也可以alloc，然后指定x y 高 宽，CGRect结构体，然后有些有代理，可以监听各种事件，有些则没有。大部分的基类都是UIView，有些是UIControl，它是继承至UIView，唯一的区别是后者可以监听事件，它是一个控制器。
* 学习UIView CAAnimation CATransition等，还有各控件的transform属性，可以做一些基础的动画，大部分是改变x y 高 宽值。然后会使用它们的block版本动画，少写很多代码。
* 学习模态视图，以及视图切换，控制器切换等。
* 学习UITableView UIPickerView UISlider UIImageView等，还有UIAlertView，太多了，就不一个一个的举例子了，比较重要，用的很多的就是UITableView UISlider UIImageView UIAlertView等，特别是UITableView，三个数据源，N个事件监听代理。
* 使用xib做自定义View

关于UIKit的学习，可以每个多试一试 ，也可以去看看头文件。

##最后的总结

一个月下来，也算是进门了，感觉它没有网上人们传的那么困难，凡事用心，一点一点的推进，就容易了，希望今年可以做自己的第一个应用上架App Store。

[使用基础动画以及UITableView制作的一个Demo](https://github.com/lcepy/geekObjective-C/tree/master/UseHMGLTransitionsAnimations)

[访问相册，摄像头，简单从网络获取资源制作的一个Demo](https://github.com/lcepy/geekObjective-C/tree/master/HttpImageAndPho)

iOS学习资料的收集

[https://github.com/lcepy/iOS-usefulinformation-zh_CN](https://github.com/lcepy/iOS-usefulinformation-zh_CN)







