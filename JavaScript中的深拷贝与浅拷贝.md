title: JavaScript中的深拷贝与浅拷贝
date: 2015-03-04 20:56:49
tags: JavaScript
---
关于拷贝的问题，主要分为深拷贝和浅拷贝，但是如果从空间分配上来说JavaScript的拷贝不应该算是深拷贝，比如：

	var d = {};
	for(k in a){
		d[k] = a[k];
	}
	return d;

今天突然想到了这么一个问题，在C语言中，所谓的拷贝，就是分两种情况，一种是把指针地址拷贝给另外一个变量，虽然也开辟的了一个内存空间，在栈上也存在着一个地址，我对这个变量进行修改，同一个指针是会改变其值的，这种拷贝叫浅拷贝。另外一种情况，直接开辟一个新空间，把需要复制的值都复制在这个新的空间中，这种拷贝叫中深拷贝。

如果看到上述的一段Js代码，很多人说它是浅拷贝，假设传入一个a对象，拷贝完成之后返回一个d，当我修改返回对象的值时并不能同时修改a对象，于是，在这里我有一个很大的疑问，在Js中到底什么是浅拷贝，什么是深拷贝的问题？

这一点上感觉Js真的很奇葩，如果在开发iOS中，不可变对象copy一下，依然是不可变，所以是浅拷贝，拷贝了指针变量中存储的地址值。如果是可变对象copy一下，到不可变，空间变化了，包括不可变mutableCopy到不可变，空间依然变化了，所以是深拷贝。但是JavaScript中对于这一点要考虑一种情况，值类型，和引用类型，这个基础知识，我相信大家都非常清楚。数字，字符串等都是值类型，object，array等都是引用类型。

	var a = [1,2,3];
	var b = a;

	b.push(4);
	console.log(a); //[1,2,3,4]
	
	var numb = 123;
	var _numb = numb;
	_numb = 567;

	console.log(numb); //123
	
从这个例子中可以看的出来，它们使用的都是＝符号，而数组a发生了变化，numb数字却没有发生变化。那么从这里，可以有一个总结，所谓了深拷贝，浅拷贝的问题，应该针对的是有多个嵌套发生的情况。不然假设是这样的情况，还能叫浅拷贝么？

	var object = {"de":123};
	var o = copy(object);
	
	o.de = 456;
	
	console.log(object) //{"de":123}
	
明显对象o中的de属性修改并没有影响到原始对象，一个对象中的属性是一个字符串，如果从内存空间的角度上来说，这里明显是开辟了新的空间，还能说是浅拷贝么？那么针对另外一种情况。

	var object = {
		"de":{
			"d":123
		}
	}
	
	var o = deepCopy(object);
	o.de.d = "asd";
	
如果一个对象中的第一层属性，不是值类型，只单层循环，这样来看的话确实是一个浅拷贝，因为在Js中引用类型用＝赋值，实际上是引用，这样说的通。所以，深拷贝，还需要做一些处理，把object，array等引用类型识别出来，深层递归到最后一层，一个一个的拷贝。

	var deepCopy = function(o){
		var target = {};
		if(typeof o !== 'object' && !Array.isArray(o)){
			return o;
		}
		for(var k in o){
			target[k] = deepCopy(o[k]);
		}
		return target;
	}
	
思路是如此，这个例子只考虑了两种情况，对象和数组，为了验证这样的思路，最后的结果与预期是一样的。

	var _copy = {
		'object':{
			'name':'wen'
		},
		'array':[1,2]

	}
	var h = deepCopy(_copy);
	h.object.name = 'lcepy';
	h.array[1] = 8;
	console.log(h);
	console.log(_copy);
	
