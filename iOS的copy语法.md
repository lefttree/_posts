title: iOS的copy语法
date: 2015-02-05 18:01:43
tags: iOS
---
这是一个关于深拷贝和浅拷贝的问题，OC设计这个语法的目的就是创建一个副本，修改副本的时候不会改变原来的对象。

> 对于这两个问题可以这么理解，浅拷贝也就是把指针赋值，大家用的都是地址。而深拷贝，则是创建了一个新的空间，虽然内容可能是一样的，但是大家使用的空间，地址都是不同的。

在OC中可以使用copy或者mutableCopy语法来实现深拷贝和浅拷贝，要求实现NSCopying或者NSMutableCopying协议。

copy和mutableCopy是有区别的，区别在于NSString和NSMutableString的区别，如果使用NSString调用copy创建出来的新对象那就是NSString，如果是使用NSString的mutableCopy创建出来的新对象那就是NSMutableString。前者是不可变的，后者是可变的，它的空间不是固定的。

	NSString *string = [[NSString alloc] initWithFormat:@"123",10];
	
	NSMutableString *str = [string mutableCopy];
	
	[string release];
	[str release];
	
遵守上面所说的创建一个副本，修改副本的时候不会改变原来的对象，其实这里可以看内存地址就可以看出来，它们是不同的。

	[str appendString:@"123"];
	
	NSlog(@"%@",string);
	NSlog(@"%@",str);

从打印的结果可以很明显的看的出来，从内存的角度上来看，因为是新对象，所以它的引用计数是加1的，所以copy或者mutableCopy都需要release。

如果使用copy语法，因为有一个机制不可变的元素，所以OC就直接把原来的对象返回，并且引用计数加1，于是这就是浅拷贝了，而mutableCopy就是深拷贝了。

如果在OC中反过来又是什么样的情况呢？比如使用NSMutableString调用它的copy方法，会产生什么样的情况呢？

	NSMutableString *string = [[NSMutableString alloc]stringWithFormat:@"123",10];
	NSString *str = [string copy];
	
如果从可变成不可变使用的copy却是一个深拷贝，产生一个新的对象，它的引用计数为1。如果可变的使用mutableCopy产生一个对象，它也是深拷贝，产生一个新对象，引用计数为1，所以都需要release。

> 总结一下，只有一种情况是浅拷贝，不可变对象调用它的copy方法为浅拷贝，再原对象上计数加1.而其他的不管是调用copy还是mutableCopy都是深拷贝，产生一个新的对象，计数为1。

###如何自定义的类提供copy或者mutableCopy

如果要自己实现自定义对象的copy或者mutableCopy语法，首先要实现NSCopying或者NSMutableCopying协议，它们的原理都是一样的，按NSCopying来讲解一个例子。

	-(id)copyWithZone:(NSZone *)zone
	{
    	Pser *copy = [[[self class] allocWithZone:zone] init];
    	copy.name = _name;
    	return copy;
	}

主要是实现copyWithZone方法，系统会为copy分配一个空间，以存储创建出来的副本。它不要求在copyWithZone内部进行内存管理，因为外部还需要使用。一般来说，使用copy还需要注意一些问题，也就是父子的问题。假设父类有实现copyWithZone方法，一般来说在子类中需要覆盖一下，然后调用它的父类的copyWithZone。而且在需要使用self class来实例对象，谁使用self就是谁，这样才能说子类调用之后，接收的还是子类，而不是父类。

	 [super copyWithZone:zone];

然后再进一步的操作。

NSMutableCopying的操作步骤是一模一样的，注意的问题也是如同。




