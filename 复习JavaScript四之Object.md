title: 复习JavaScript四之Object
date: 2015-01-25 21:42:07
tags: node&js
---
JavaScript的所有对象都衍生于Object对象，所有对象都继承了Object.prototype上的方法和属性，虽然它们可能会被覆盖，熟悉它对于编程能起到很大的作用，也能比较深刻的了解JavaScript这门语言。

[可参考的资料-Object](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)

创建一个对象可以使用new，也可以使用快速创建的方式：

	var _object = {};

_object对象中就可以使用Object.prototype中所有的方法和属性，虽然看起来它是空的。说到这里在编程中常常有一个非常有用的需求，如何判断一个对象是空对象。

这是zepto中的判断一个对象是否是空对象，常常使用：

	$.isEmptyObject = function(obj) {
    	var name
    	for (name in obj) return false
    	return true
  	}

也顺便看了下jQuery原理是一模一样的：

	isEmptyObject: function( obj ) {
		var name;
		for ( name in obj ) {
			return false;
		}
		return true;
	}
使用in操作符来实现，它不会遍历到父原型链。

constructor返回一个指向创建了该对象的函数引用，这个东西主要是可以用来识别（类）到底是指向哪里的。

defineProperty直接在一个对象上定义一个新属性，非常适合用于动态构建，传入三个参数［动态添加对象的目标对象，需要定义或被修改的属性名，需要定义的对象］，在第三个参数中可以有些属性来表示是否继承（__proto__），要不要定义get，set方法，enumerable是否可枚举。

defineProperties跟上述defineProperty一样，但是它可以添加多个。

getOwnPropertyNames返回一个由指定对象的所有属性组成的数组

keys返回一个数组包括对象所有的属性（可枚举）

> keys是经常会用到的一个属性，它只能包可枚举的，如果想获取一个对象的所有属性包括不枚举的，那么使用getOwnPropertyNames。

hasOwnProperty用于判断某个对象是否包含有自身的属性，这个方法常常用于检测对象中的属性是否存在，它只检测自身，对于继承过来的都是false，这一点是非常重要的理解。

isPrototypeOf 用于检测一个对象是否在另一个对象的原型链上，比如有两个对象是互相交互的，常常会使用它来进行检测。

propertyIsEnumerable这个方法也比较重要，返回一个布尔值，检测一个对象的自身属性是否可以枚举

> 可枚举的理解，也就是对象的属性可枚举，它的属性值不可以修改，但是在Js中它有自己的定义，引擎内部看不见的该属性的[[Enumerable]]特性为true，那么就是可枚举的。基本上把一个普通对象可以看做是一个枚举类型，比如var color = {'red':1}，red是可以修改的，但是red是可枚举的，但是如果是继承过来的属性，propertyIsEnumerable是返回false的，它还有一个特点，就是自身。

如果要定义不可枚举的属性，那就要使用defineProperty方法了，目前不能用对象直接量或者构造函数定义出来。

	var obj = {name: 'jack', age:23}
	Object.defineProperty(obj, 'id', {value : '123', enumerable : false });
	
