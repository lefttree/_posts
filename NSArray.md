title: NSArray
date: 2015-05-03 15:42:06
tags: iOS
---

基础扎实才能使编程更加的得心应手，今天要写一些学习NSArray以及NSMutableArray的东西。

创建一个数组其实跟创建NSString一样OC也提供了三种方式，语法糖，实例化，类方法。

	//创建语法糖
    NSArray *arry = @[@"1",@"2",@"3",@"4"];
    //手动实例创建
    NSArray *arry1 = [[NSArray alloc] initWithObjects:@"1",@"2", nil];
    //类方法创建
    NSArray *arry2 = [NSArray arrayWithObjects:@"1",@"2", nil];

查找一个元素在数组中是否存在

	//查找元素是否存在，在数组中
    BOOL isTrue = [arry containsObject:@"1"];
    
获取数组的长度

	//数组的长度
    NSUInteger count = arry.count;
    NSLog(@"%zd",count);
 
获取第一个元素和获取最后一个元素

	//获取第一个元素
    id first = arry.firstObject;
    //获取最后一个元素
    id last = arry.lastObject;

查找元素在数组中的位置，还可以指定一个range来确定查找的范围

	//查找元素在数组中的位置
    NSInteger index = [arry indexOfObject:@"1"];
    NSLog(@"%zd",index);
    
    //查找元素的位置，可以指定一个range
    NSRange arrRange = NSMakeRange(1, 2);
    NSInteger rangeIndex = [arry indexOfObject:@"3" inRange:arrRange];
    NSLog(@"%zd",rangeIndex);
    
指定一个下标来获取元素

	//指定一个下标获取元素
    id element = [arry objectAtIndex:1];
    NSLog(@"%@",element);
    
添加一个元素**注意 NSArray是不可变的，所以添加的元素不是旧数组，而是创建了一个包含旧数组的新数组**，还可以添加一个数组

	//添加一个元素，返回一个新的数组
    NSArray *lcepy = [arry arrayByAddingObject:@"lcepy"];
    //添加一个数组，返回一个新的数组
    NSArray *newLcepy = [arry arrayByAddingObjectsFromArray:@[@"lcepy",@"github.io"]];
    
格式化一个数组

	//通过一个分隔字符串来组建一个拼接字符串
    NSString *ress =  [newLcepy componentsJoinedByString:@"."];
    NSLog(@"%@",ress);
    
除了for循环，for in循环外的其它几种常用的遍历方式

	//NSEnumerator是一个抽象类，可以使用它的nextObject来遍历元素，通常和while一起使用
    NSEnumerator *enumer =  [newLcepy objectEnumerator];
    BOOL isEnumer = YES;
    while (isEnumer) {
        id enumerValue = [enumer nextObject];
        if (!enumerValue) {
            isEnumer = NO;
        }
        NSLog(@"%@",enumerValue);
    }
    
    [arrays enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"%@",obj);
        if (idx > 3) {
            *stop = YES;
        }
    }];
    
假设如果数组中的元素都想向某个方法发送消息，那么可以这么使用，这样的方式，比如在想移除所有子视图这样的场景中非常好用

	//假设有一个数组中的元素都想向某个方法发送消息，那么可以使用makeObjectsPerformSelector类定义了
    [newLcepy makeObjectsPerformSelector:@selector(length)];
    
给数组排序的几种方式

	//排序
    NSArray *arrays = @[@1,@2,@0,@8,@3,@9,@5];
    
    //正
    NSArray *minMax = [arrays sortedArrayUsingComparator:^NSComparisonResult(id obj1, id obj2) {
        return obj1 > obj2;
    }];
    NSLog(@"%@",minMax);
    //倒
    NSArray *maxMin = [arrays sortedArrayUsingComparator:^NSComparisonResult(id obj1, id obj2) {
        return obj1 < obj2;
    }];
    NSLog(@"%@",maxMin);
    
	//    [arrays sortedArrayWithOptions:<#(NSSortOptions)#> usingComparator:<#^NSComparisonResult(id obj1, id obj2)cmptr#>]
    
	//    [arrays sortedArrayUsingFunction:<#(NSInteger (*)(__strong id, __strong id, void *))#> context:<#(void *)#>]
    
    SEL execu = @selector(compare:);
    NSArray *newArr = [arrays sortedArrayUsingSelector:execu];
    NSLog(@"s%@",newArr);
    
数组还可以进行筛选，NSPredicate这个类在使用Core Data时会经常用到

	[arrays filteredArrayUsingPredicate:<#(NSPredicate *)#>]

###NSMutableArray

创建NSMutableArray只有两种方式，alloc和类方法，这是一个可变的数组，可以增删改滴，也可以排序，NSArray存在的方法，NSMutableArray全部具备，只是它多了可以添加，删除，修改，替换的方法，这里不一一写例子了，可以自己实际操作一下。

	- (void)addObject:(id)anObject;
	- (void)insertObject:(id)anObject atIndex:(NSUInteger)index;
	- (void)removeLastObject;
	- (void)removeObjectAtIndex:(NSUInteger)index;
	- (void)replaceObjectAtIndex:(NSUInteger)index withObject:(id)anObject;
	- (void)addObjectsFromArray:(NSArray *)otherArray;
	- (void)exchangeObjectAtIndex:(NSUInteger)idx1 withObjectAtIndex:(NSUInteger)idx2;
	- (void)removeAllObjects;
	- (void)removeObject:(id)anObject inRange:(NSRange)range;
	- (void)removeObject:(id)anObject;
	- (void)removeObjectIdenticalTo:(id)anObject inRange:(NSRange)range;
	- (void)removeObjectIdenticalTo:(id)anObject;
	- (void)removeObjectsFromIndices:(NSUInteger *)indices numIndices:(NSUInteger)cnt NS_DEPRECATED(10_0, 10_6, 2_0, 4_0);
	- (void)removeObjectsInArray:(NSArray *)otherArray;
	- (void)removeObjectsInRange:(NSRange)range;
	- (void)replaceObjectsInRange:(NSRange)range withObjectsFromArray:(NSArray *)otherArray range:(NSRange)otherRange;
	- (void)replaceObjectsInRange:(NSRange)range withObjectsFromArray:(NSArray *)otherArray;
	- (void)setArray:(NSArray *)otherArray;
	- (void)sortUsingFunction:(NSInteger (*)(id, id, void *))compare context:(void *)context;
	- (void)sortUsingSelector:(SEL)comparator;
	- (void)insertObjects:(NSArray *)objects atIndexes:(NSIndexSet *)indexes;
	- (void)removeObjectsAtIndexes:(NSIndexSet *)indexes;
	- (void)replaceObjectsAtIndexes:(NSIndexSet *)indexes withObjects:(NSArray *)objects;

	- (void)setObject:(id)obj atIndexedSubscript:(NSUInteger)idx NS_AVAILABLE(10_8, 6_0);

	- (void)sortUsingComparator:(NSComparator)cmptr NS_AVAILABLE(10_6, 4_0);
	- (void)sortWithOptions:(NSSortOptions)opts usingComparator:(NSComparator)cmptr NS_AVAILABLE(10_6, 4_0);