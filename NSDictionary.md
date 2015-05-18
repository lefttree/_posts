title: NSDictionary
date: 2015-05-16 19:20:01
tags: iOS
---

使用字典，能很好的组织数据，OC中的字典有三种方式

	//创建字典
    NSDictionary *dic = [[NSDictionary alloc] initWithObjectsAndKeys:@"value",@"key",@"github",@"build", nil];
    NSDictionary *dic1 = @{@"key1":@"value1"};
    NSDictionary *dic2 = [NSDictionary dictionaryWithObjectsAndKeys:@"value2",@"key2", nil];
    
包含多少个键值对

	//包含多少个键值对
    NSLog(@"%zd",dic.count);
    
通过已知的key名来获取值

	//已知key来获取值
    NSString *value = [dic objectForKey:@"key"];
    NSLog(@"%@",value);
    
获取所有的key和获取所有的值

	//获取所有的key名或者值，返回一个不可变数组
    NSArray *keys = [dic allKeys];
    NSArray *values = [dic allValues];
    NSLog(@"%@",keys);
    NSLog(@"%@",values);
    
通过一个数组key获取值

	NSArray *vals = [dic objectsForKeys:@[@"key"] notFoundMarker:@"not"];
    NSLog(@"%@",vals);
    
遍历

	[dic enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
        NSLog(@"%@",key);
        NSLog(@"%@",obj);
    }];
    
排序

	NSArray *kesArray =  [dic keysSortedByValueUsingComparator:^NSComparisonResult(id obj1, id obj2) {
        NSString *ke1 = obj1;
        NSString *ke2 = obj2;
        return [ke1 compare:ke2];
    }];
    
过滤

	[dic keysOfEntriesPassingTest:<#^BOOL(id key, id obj, BOOL *stop)predicate#>];
	
Mutable版的字典，多了增删的方法

	NSMutableDictionary *dicmutable = [NSMutableDictionary dictionaryWithDictionary:dic];

添加

    [dicmutable setObject:@"icepy" forKey:@"io"];
    NSLog(@"%@",dicmutable);
删除

    [dicmutable removeObjectForKey:@"build"];
    NSLog(@"%@",dicmutable);