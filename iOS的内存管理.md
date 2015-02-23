title: iOS的内存管理
date: 2015-02-04 23:00:33
tags: iOS
---
在iOS开发中使用的是引用计数的方式来管理内存，也就是跟踪记录每个值被引用的次数。当声明一个变量来接收一个指针对象，引用次数就＋1，如果指针对象被添加到数组，字典，或者被其他指针对象所使用又＋1.如果使用完毕之后对它进行－1，一直到0，系统会立马释放这段内存。


>如果开启了ARC机制，编译器会帮助我们在代码中添加上内存管理的代码，而且在Xcode中比如retain这些都是不可以使用的

在OC中可以使用retainCount来查看指针对象的引用次数。

	Wower *wower = [[Wower alloc] initWithName:@"lcepy" andT:@"魔兽世界"];
    NSLog(@"%zi",[wower retainCount]);
	
一般来说如果在代码中使用了alloc，copy，retain，引用计数都是加一，而release则是引用计数减一，又比如把指针对象添加到数组，字典，内部的引用计数也会加一，但是这个的话，系统是会来管理的。又比如一些静态方法，也会由系统来管理，内部是会加一的。还有一种叫做自动释放池的玩意，autorelease，它的功能是整个执行过程结束，系统来管理释放的工作。

一般来说基本类型的数据也就是C语言的如int这样的，直接赋值即可不用管理，在iOS中主要是对OC指针对象进行内存管理。在使用@property的时候，根据参数retain，copy，编译器会帮助我们生成getter，setter方法，而且还会帮助我们进行一下内存管理，比如：

	-(void)setterFound:(Found *)found
	{
    	if (_found != found) {
        	[_found release];
        	_found = found;
        	[_found retain];
    	}
	}

有一点要注意的是如果是retain，copy一般来说接收的都是OC对象，基本类型可以用assign。

实例：

	//.h文件
	@interface Found : NSObject

	@end


	@interface Wower : NSObject
	{
    	Found *_found;
	}

	@property(nonatomic,copy) NSString *name;
	@property(nonatomic,copy) NSString *game;

	-(id)initWithName:(NSString *)name andT:(NSString *)game;
	-(void)setterFound:(Found *)found;

	@end
	//.m文件
	@implementation Wower

	-(id)initWithName:(NSString *)name andT:(NSString *)game
	{
    	self = [super init];
    	if (self) {
        	_name = name;
        	_game = game;
    	}
    	return self;
	}


	-(void)dealloc
	{
    	
    	NSLog(@"Found的引用计数%zi",[_found retainCount]);
    	[_found release];
    	NSLog(@"Found的引用计数%zi",[_found retainCount]);
    	[super dealloc];
    
	}

	-(void)showMessage
	{

    	NSLog(@"%@",_name);
    	NSLog(@"%@",_game);
	}

	-(void)setterFound:(Found *)found
	{
    	if (_found != found) {
        	[_found release];
        	_found = found;
        	[_found retain];
    	}
	}
	@end

	@implementation Found

	-(void)dealloc
	{
    	[super dealloc];
    	NSLog(@"释放");
	}

	@end
	
	//main文件
	
	int main(int argc, const char * argv[]) {
    	@autoreleasepool {
        	Wower *wower = [[Wower alloc] initWithName:@"lcepy" andT:@"魔兽世界"];
        	NSLog(@"%zi",[wower retainCount]);
        	Found *f = [[Found alloc] init];
        	[wower setterFound:f];
        	[wower release];
        	[f release];
    	}
    	return 0;
	}


dealloc是OC对象释放之后，系统会自动调用，这里一般都是来释放一些其他对象。在Wower类的dealloc中只释放了_found，而copy的那个却没有释放，那是因为@""快速创建的，一般来说这些都不需要自己来管理而是系统。

>关于OC的内存管理，其实有一个原则，就是谁创建谁释放，谁使用谁释放。在之前，创建了wower,f对象，于是它们要在外部释放，而我使用了setFound方法来把f对象设置到成员变量中，于是这里就成了wower对象的_found成员变量在使用，于是它有责任在dealloc的时候来把f对象的引用计数减1。


	
