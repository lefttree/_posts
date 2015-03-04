title: 复习JavaScript十之理解引用类型和匿名函数闭包
date: 2015-02-03 19:02:01
tags: JavaScript
---

引用类型虽然看起来和类很相似，但是它们却是不同的概念，引用类型的值，也就是对象是引用类型的一个实例。在Js中引用类型主要有Object，Array，Date，正则，Function等。

Object之前已经复习过了，Array是用的非常频繁的一个引用类型。

**Array**

在Js中数组可以存储任意的数据，而且它的大小是可以动态调整的类似于OC中的NSMutableArray。创建数组可以使用构造函数的方式也可以使用字面量的形式，另外可以使用concat从一个数组中复制一个副本出来。数组本身提供了很多方法让开发者使用来操作数组。

* length 数组的长度
* toString  可以返回一个以，拼接的字符串，相当于是调用了下join(',')
* join 可以用一个分割符来拼接成一个字符串
* push 添加一个数据到数组的末端
* pop 删除数组中的最后一项，有返回值
* shift 删除数组的第一项，有返回值
* unshift 添加一个数据到数组的首端
* reverse 倒序
* sort 可以传入一个排序的函数
* slice 可以基于当前数组返回一个新的数组，接收两个参数，返回项的起始位置和结束位置
* splice 可以传入N个参数，第一个参数表示要删除，插入或则替换的位置，第二个参数表示要删除的项数，第三个到第N个表示要插入或则替换的数据

**Date**

时间对象也是使用非常多的玩意，它是使用GMT时间来描述，而且时间对象是可以直接比对大小的。

	var date1 = new Date(2015,1,2);
	var date2 = new Date(2015,1,10);
	
	date1 < date2  

常用的方法
	
* getTime 获取时间对象的毫秒数
* setTime 设置时间对象的毫秒数，会改变日期
* getFullYear 获取时间对象的年（2015）
* getMonth 获取时间对象的月（需要加1）
* getDay 获取日期的星期几（0-6）星期天到星期六
* getDate 获取日期的天数
* getHours 获取当前日期的小时
* getMinutes 获取当前日期的分钟数
* getSeconds 获取当然日期的秒数

上面看起来都是获取，当然也有设置，只是相应的get置换成set即可。

**正则表达式**

在Js里正则表达式是用RegExp类型来支持的，关于正则可以看看之前写的一篇文章，用python来描述的如何读懂正则。

Js也支持三种模式，gim，表示全局，不区分大小写，多行。

一般来说很少有人这么使用var xxx = new RegExp()，而是用字面量的方式，比如var xx = /[bc]/gi;像用的比较多的方法有exec用于捕获包含第一个匹配项的数组，没有则返回null。test，用于判断，如果匹配返回true，不匹配返回false。

**Function**

说起来Js的核心是什么？那就是函数了。对于函数主要是理解它的几个概念，它可以当值来传递，没有重栽。而且声明的时候，比如function a(){} var a = function(){} 执行时会有区别。函数内部的参数arguments包含了传入的所有参数，this，表示在这个函数内的作用域，以及prototype。

**处理字符串**

在Js中还有一种叫做包装类型的玩意，正因为此所以处理一些基本数据类型，比如字符串时，有很多方法可以使用。

* concat 可以将一个或者多个字符串拼接起来，返回一个新的字符串
* slice 接收两个参数，起始位置和结束位置，返回一个新的字符串
* substr和substring和slice一样，唯一的不同是substr第二个参数是返回字符串的个数
* indexOf 从头开始查询字符串，存在会返回它所在的位置，没有返回－1
* lastIndexOf 从最后开始查询字符串
* toUpperCase 转大写
* toLowerCase 转小写
* match 正则表达式使用跟exec一样
* search 正则表达式使用，查询到返回一个位置，没有返回－1
* replace 替换，第一个参数可以是正则表达式也可以是字符串，第二个参数是要替换的字符串
* localeCompare比较字符串，如果字符串相等返回0，如果字符串的字母排在参数字符串之前，返回负数，如果是之后，返回正数。

###理解匿名函数和闭包

匿名函数又叫拉姆达函数，主要是在把函数当值传递的时候用，或者是把函数当返回值，比如：

	function d(callback){
		callback();
	}
	d(function(){
		alert('123')
	});
	
	//或者
	
	function b(){
		return function(){
			alert('123');
		}
	}
	var g = b();
	g();
	
其实第二种方式跟闭包的意义一样了，所谓的闭包书面的解释是可以访问另一个函数作用域内变量的函数，稍微改写一下可能会更明显。

	function b(){
		var name = '123';
		return function(){
			alert(name);
		}
	}
	var g = b();
	g();

从这里可以看出来return的函数可以访问到name，而外部却不行，这个返回值的函数就可以理解为闭包。理解闭包还可以看一个经典的求值的例子。

	function save_i(){
		var a = [];
		for(var i = 0;i<10;i++){
			a[i] = function(){
				return i;
			}
		}
		return a;	
	}

	var c = save_i();
	for(var i = 0;i<10;i++){
		alert(c[i]());
	}
	
从这个例子上来看，我们想得到的结果是10次循环a[i]保存着一个闭包，然后alert出从0到10，但是结果很出人意料，全部是10，为什么？哪里理解的不对呢？a[i]明明是内部函数，然后让它访问另外一个函数作用域内的变量i。

个人觉得可以这样去分析问题，在客户端执行Js时有一个全局执行环境，指向的是window对象。而所谓的对象也就是引用类型，实际上在后台执行环境中，它就是一个指针。

回到Js当代码在执行的时候，会创建变量对象并且构建一个作用域链，而这个对象保存着当前函数可以访问的对象。

	window
		->save_i
			->this|argument
			->a
			->i
			->看不见的a[0]-a[10]
			->a[0]function(){}
				->i
	    ->c
	    
上述的i和a[0]里的i是同一个i，那么结果就是10。

进一步处理

	function save_i(){
		var a = [];
		for(var i = 0;i<10;i++){
			a[i] = function(k){
				return function(){
					return k;
				};
			}(i)
		}
		return a;	
	}

	var c = save_i();
	for(var i = 0;i<10;i++){
		console.log(c[i]());
	}

接着按上面的节奏来分析

	window
		->save_i
			->this|argument
			->a
			->i
			->看不见的a[0]-a[10]
			->a[0]function(){}
				->k
				->function(){}
					->k
				
	    ->c

什么是传参？按值传递，相当于是在那个立即执行的函数中创建了一个新的地址和空间，虽然值是一样的，但是每一个k又是不同的，所以得到的结果正好满足了我们的预期。

本来正常情况下save_i执行完毕后就要销毁，但是内部的闭包被包含在这个作用域内了，所以save_i没法销毁，从这里可以看的出来闭包会带来内存的问题，因为用完之后没法销毁，如果不注意的话。

那么用完之后只能设置为null来解除引用，等着自动销毁把内存回收。




