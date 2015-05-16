title: CoreData的使用
date: 2015-05-05 10:31:04
tags: iOS
---

CoreData是Cocoa中处理数据，绑定数据的一个框架，如果不靠谱数据的平台一致性（iOS，Android），完全可以使用它替代SQLite。虽然，iOS也有一个C实现的SQLite库，但是语法层面比较琐碎，而且使用的是C函数。如果要使用，大家可以选择使用一个开源的库来替代[Obje-C版](https://github.com/ccgus/fmdb)和[Swift版](https://github.com/FahimF/SQLiteDB)

从代码层面上来看，要创建一个CoreData实例，需要四个步骤

* 创建数据库文件的物理地址
* 创建模型托管对象
* 创建持久化存储调度器
* 创建上下文

正常情况下，上述四个步骤，我们只使用一次，建议惰性加载它

	-(NSManagedObjectContext *)context
	{
    	if (!_context) {
        	NSError *error = nil;
        	//创建db url
        	NSString *dbPath = [self.baseDoc stringByAppendingPathComponent:@"manong.db"];
        	NSURL *dbUrl = [NSURL fileURLWithPath:dbPath];
        	//创建模型托管对象
        	NSManagedObjectModel *model = [NSManagedObjectModel mergedModelFromBundles:nil];
        	//创建持久化存储调度器
        	NSPersistentStoreCoordinator *store = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:model];
        	[store addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:dbUrl options:nil error:&error];
        	//创建上下文
        	_context = [[NSManagedObjectContext alloc] init];
        	[_context setPersistentStoreCoordinator:store];
    	}
    	return _context;
	}

下面给出一张关系图

![CoreData类关系图](http://websources.qiniudn.com/iOS/CoreData类关系图.png)

从上述的图中，其实可以看到一些有关系的模块

* Managed Object Model 用于描述数据模型，这里包括实体，属性，请求等
* Managed Object Context 参与对数据对象进行各项操作的上下文
* Managed Object 数据对象（用户使用的对象与上下文有关联）
* Persistent Store 相当于数据库文件的管理器，处理底层对数据库文件的读取与写入

**基本上创建一个Managed Object文件不需要自己去手动创建，而且建议Xcode帮助我们创建的model文件不要修改**

如果非要自己创建的话，那么继承NSManagedObject类吧

	#import <Foundation/Foundation.h>
	#import <CoreData/CoreData.h>

	@interface ManongTag : NSManagedObject

	@property (nonatomic, retain) NSNumber * contentCount;
	@property (nonatomic, retain) NSString * tagKey;
	@property (nonatomic, retain) NSString * tagName;

	@end

#下面给出一张实际的操作图，如何使用CoreData。

![CoreData操作图形化](http://websources.qiniudn.com/iOS/CoreData操作图形化.jpg) 

* 首先add Entity添加一个实体
* 然后在Attributes中添加相应的属性，在Type中选择相应的数据类型
* Pelationships可以对实体进行关联，比如ManongTitle实体中关联了一个ManongContent实体
* <橘黄色箭头>可以设置多关联，在使用的时候它会是一个NSSet集合
* Style可以查看设计图

如果想新增一条数据，需要三个步骤才能实现，第一先创建一个实体描述，第二创建一个ManagedObject对象，第三给上下文的save方法发送消息。

	NSEntityDescription *mnTag = [NSEntityDescription entityForName:@"ManongTag" inManagedObjectContext:self.context];
    ManongTag *manongTag = [[ManongTag alloc] initWithEntity:mnTag insertIntoManagedObjectContext:self.context];
    manongTag.tagKey = tagKey;
    manongTag.tagName = tagNmae;
	NSError *error = nil;
    BOOL success;
    [self.context save:&error];
    success = error ? NO : YES;
    
如果想在实体中查询数据，一般情况下两个步骤，如果是条件查询最少三个步骤，第一创建一个查询请求，第二创建一个谓词对象，第三给上下文的executeFetchRequest方法发送消息。

	//查询所有的数据
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"ManongDigest"];
    NSArray *arr = [self.context executeFetchRequest:request error:&error];
    
    //条件查询
    NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@“ManongDigest”];
    NSPredicate *dicate = [NSPredicate predicateWithFormat:@"%K BEGINSWITH[c] %@",@"tagName",@"a"];
    NSArray *arr = [self.context executeFetchRequest:request error:&error];
    
如果想更新某一条数据，正常情况下三个步骤，第一创建一个查询请求，第二修改MangedObject对象的属性，第三给上下文的save方法发送消息。

	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"ManongDigest"];
    NSArray *arr = [self.context executeFetchRequest:request error:&error];
    for(ManongDigest *digest in arr)
    {
    	digest.tagName = @"lcepy";
    }
    NSError *error = nil;
    [self.context save:&error];
    if (error) {
        return NO;
    }else{
        return YES;
    }

如果想删除某一条数据
	
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"ManongDigest"];
    NSArray *arr = [self.context executeFetchRequest:request error:&error];
	if(arr.count){
		ManongDigest *digest = (ManongDigest *)arr[0];
		[self.context deleteObject:digest];
	}

上述四种操作方式是最常用的，当然CoreData 的API肯定不止这四种，但是使用的逻辑思维基本上是，查询－处理－使用上下文或者创建实体－处理－使用上下文。

这个框架算比较复杂的一个，学习起来曲线也比较大，需要耐心。

参考资料

* [objc-CoreData](http://www.objc.io/issue-4/)
* [一个完整的 Core Data 应用](http://objccn.io/issue-4-2/)
* [Core Data API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CoreData_ObjC/index.html)
* [NSPredicate API](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSPredicate_Class/)
