title: 理解UITableView
date: 2015-02-06 21:06:07
tags: iOS
---
UITableView是在iOS开发中使用非常频繁的一个控件，比如QQ的消息人列表，淘宝的商品，微信等等都会使用到它，对于它的理解有一个前提，那就是要理解iOS MVC的设计思路。

创建一个UITableView可以使用代码的方式，也可以使用xib或者storyboard。

	UITableView *tableView = [[UITableView alloc] initWithFrame:[[UIScreen mainScreen] applicationFrame]];

在MVC的设计思想中控制器控制着要从模型中拿数据，然后把数据装载给视图。在UITableView中模型主要是实现在UITableViewDataSource数据源中，也就是UITableView有几行，分组么，然后每一行装载什么？数据源的目的就是想问开发者这些问题。

	-(NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
	{

	}

这个方法如果存在有分组，系统就会询问，有几个分组？如果不是分组的，可以不实现这个方法，系统默认是1。

有了分组之后，然后系统会询问会有几行？

	-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
	{

	}

section跟分组有关

最后系统询问，每一行实现什么？

	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
	{

	}
	
这个方法的实现可以说是核心的核心，在UITableView中每一行是要求实现一个cell，可以想象每一个cell相当于就是一个容器，这个容器里面装载着很多其他UI对象，当然cell也可以自定义。

考虑到性能，其实每一个cell可以从缓存中读取

	UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cell"];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewStylePlain reuseIdentifier:@"cell"];
    }
    
    //do

indexPath是一个类，看看头文件就可以明白，它这个里面装载着row，section等，这些数值非常有意义，在从模型里获取数据的时候。数据源还有一些其他的方法可以实现，比如编辑模式，这个东西一般用于删除每一条消息，从右向左滑动的时候。

	-(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath
	{
	
	}

排序

	-(void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath
	{
	
	}
	
有了数据源，当然也会存在一些代理，这些代理就是一些动作，比如实现了某些代理方法之后，系统会询问，你点了？一个手指按下不动了？等等，然后再doing什么，就是写在这些代理的方法中，主要是实现UITableViewDelegate。

常用的比如按下的时候，放开的时候：

点击了每一行时做的动作

	-(void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
	{
	
	}

放开每一行时做的动作

	-(void)tableView:(UITableView *)tableView didDeselectRowAtIndexPath:(NSIndexPath *)indexPath
	{
	
	}

协议中有很多在某些情况下会非常有用，细节就要慢慢在学习中积累了。
	

