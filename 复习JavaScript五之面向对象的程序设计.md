title: 复习JavaScript五之面向对象的程序设计
date: 2015-01-26 14:52:39
tags: JavaScript
---
面向对象的语言有一个非常明显的标志：类，通过类来创建任意多个具有相同属性和方法的对象，可惜的是Js里没有这样的概念。

但是Js有一个特性：一切皆是对象。

聪明的开发者通过这些特性进行摸索，于是迂回发明了一些程序设计，以便更好的组织代码结构。

###工厂模式

主要是用来解决有多个相同属性和方法的对象的问题，可以用函数来封装特定的接口来实现

	var computer = function(name,version){
		return {
			'name':name,
			'version':version,
			'showMessage':function(){
				alert(this.name);
			}
		}
	}
	var test = computer('apple','11.1');
	test.showMessage();

###构造函数模式

我们知道像原生的构造函数，比如Object，Array等，它们是在运行时自动出现在执行环境中的。因此，为了模仿它，这里也可以通过一个普通的函数，并且new出一个对象，这样就成为了自定义的构造函数，也可以为他们添加自定义的属性和方法。

但是这样的构造函数有一个缺陷，就是每个方法都会在每个实例上创建一次，因为每次创建都需要分配内存空间，但是有时候这样的特性还是有用的，主要是要控制它们，在不使用的时候释放内存。

	var Computer = function(name,version){
		this.name = name;
		this.version = version;
		this.showMessage = function(){
			alert(this.name);
		}
	}
	
	var apple = new Computer('apple',2014);
	var dell = new Computer('dell',2010);
	apple.showMessage();
	dell.showMessage();

像apple，dell是通过Computer实例化出来的不同的对象，但是它们的constructor都是指向Computer的。这里也可以使用instanceof来对（对象）进行检测。

在书写上构造函数跟其他函数是没有什么区别的，主要的区别还是在使用上，构造函数需要使用new操作符。

其实这样的书写，已经跟类没有什么区别了，表面上来看，而构造函数我个人更倾向于一个类的某个静态方法。

###原型模式

说到原型模式就不得不提一提关于指针的问题，因为每一个函数都有一个prototype属性，而这个属性是一个指针，指向一个对象。

**C语言描述指针，这个在iOS开发中非常重要**

比如我先定义一个int类型的指针变量和一个普通的int类型数据，然后给指针变量赋值。

	int *p;
	int pp = 123;
	p = &pp;
	*p = 999;
	printf('%d',pp);

*是一个特殊符号用于标明它是一个指针变量。

&是地址符

分析这个就要说到栈内存和堆内存了，比如*p在栈内存中分配了一个地址假设是ff22x0，它还没有空间。而pp存在一个地址ff23x0，并且分配了一个空间存储着123，这个地址是指向这个空间的。

p = &pp 这样的赋值操作，也就是把ff23x0取出来，并且给p分配一个空间把ff23x0存储进去，并且ff22x0指向这个空间。

*p = 999 从这里就可以看出来p操作的是地址，而这个地址不就是ff23x0么，于是pp成了999。

所谓的指针也就是存储着地址的变量。

回到原型上，如果每一个函数中的 prototype属性都是一个指针，实际上它只是一个地址引用着一个空间，而这个空间正是我们写的xxx.prototype.xxx = function(){}这样的代码在运行时分配的空间。那么可见，使用原型的好处是空间只分配一次，大家都是共享的，因为它是指针。

先看一个例子

	var Computer = function(name){
		this.name = name;
	}
	Computer.prototype.showMessage = function(name){
		alert(name);
	}
	
	var apple = new Computer('apple');
	var dell = new Computer('dell');
	Computer.prototype.isPrototypeOf(apple);
	
在解释这个原型链之前，还要明白Js的一个特性，就是如果自身不存在，它会沿着原型往上查找。它的原理稍微有些绕，Computer自身的prototype是指向它自身的原型对象的，而每一个函数又有一个constructor指向它自身，prototype.constructor又指向它自身。于是Computer的两个实例apple，dell内部有一个__proto__属性是指向Computer.prototype的，最后的结果是它们可以使用showMessage方法。

当然它们还有一个搜索原则，比如在调用showMessage的时候，引擎先问apple自身有showMessage吗？“没有”，继续搜索，apple的原型有吗，“有”，调用。所以从这里可以看出，this.showMessage是会覆盖prototype.showMessage的。

另外还可以使用isPrototypeOf来检测一个对象是否在另一个对象的原型链上，上述的代码返回的是true。

	apple.hasOwnProperty('name')
	
	apple.hasOwnProperty('showMessage')
	
使用hasOwnProperty来检测到底是对象属性还是原型属性，使用this创建的属性是一个对象属性。
<!--more-->

从上面可以看出来原型链的好处，但是它也不是万能的，正因为指针的存在，对于某些引用类型来说这个就非常不好了，我需要保持原对象属性值是每一个对象特有的，而不是共享的，于是把之前的构造函数与原型组合起来，也就解决了这样的问题。

	var Computer = function(name){
		this.name = name;
	}
	Computer.prototype.showMessage = function(){
		alert(this.name);
	}
	
	var apple = new Computer('apple');
	apple.showMessage();
	
这样的结果是在对象中都会创建一份属于自己的属性，而方法则是共享的。

**动态原型模式**

有时候遇到某些问题需要动态添加原型，但是实例中是不能添加的，所以绕来一下，在初始化构造函数中添加。

	var Computer = function(){
		if(typeof this.showMessage !== 'function'){
			Computer.prototype.showMessage = function(){
				
			}
		}
	}
只要初始化了一次，以后就不用修改了。

###寄生构造函数模式

这种模式的原理就是在一个函数中封装需要创建对象的代码，然后返回它。

	var test = function(name){
		return {
			'name':name
		}
	}
	var g = new test('apple');
	var f = de('dell');

看起来它跟工厂模式还是很像的，

###稳妥模式

这种模式主要是在解决需要安全的环境中使用，一般来说一个类如果不提供getter，setter方法，是不允许直接访问和修改的。

	var computer = function(name){
		var _name = name;
		return {
			'getter':function(){
				return _name;
			},
			'setter':function(name){
				_name = name;
			}
		}
	}
这样的方式可以保证属性或者说是数据的安全性，不允许直接随便修改，如果不提供setter方法的话，压根就不允许。

###继承

谈到面向对象，那么就不能不谈谈继承的问题了，而在Js中主要是将原型作为实现继承的主要思路。

	var Computer = function(name){
		//this.name = name;
	}
	Computer.prototype.show = function(){
		alert('computer')
	}
	
	var Apple = function(){
	
	}
	Apple.prototype = new Computer();
	Apple.prototype.hide = function(){}
	Apple.prototype.show = function(){
		alert('apple')
	}
	var apple = new Apple();
	apple.show();
	alert(apple instanceof Computer);

使用这样的方式，实际上是从Computer的实例中先借它的prototype中所有的方法，但是这里会存在几个问题。

* 如果Computer中需要传入参数，比如name，借的时候我根本不知道要传入什么参数。
* 在Apple中如果要继续给原型添加方法，那么就不能使用字面量的形式了，它会覆盖掉
* 如果要重写父类中的方法必须要在借prototype之后
* 那么如何确定原型和实例的关系？貌似用instanceof和isPrototypeOf都会返回true

**解决问题一如何传入参数**

我们知道Js中有两个方法可以改变函数的上下文，apply和call，实际上类就是函数，这里既借属性也借prototype，不就可以解决这样的问题了么。

	var Computer = function(name){
		//this.name = name;
	}
	Computer.prototype.show = function(){
		alert('computer')
	}
	
	var Apple = function(name){
		Computer.call(this,name);
	}
	Apple.prototype = new Computer();
	
	var apple = new Apple('apple');
	alert(apple instanceof Apple);
	alert(apple instanceof Computer);
	
在运行时先借prototype，然后再借子类的this，但是这个也有个问题，那就是会调用两次父类。

**继承的技巧**

还有一种继承是生成一个临时对象，然后临时对象借需要继承的父类的prototype。

	var extend = function(o){
		var F = function(){}
		F.prototype = o;
		return new F();
	}
	
	var parent = {
		'name':['lcepy']
	}
	
	var game = extend(parent);
	game.name.push('wow');
	
	var _game = extend(parent);
	_game.name.push('view');
	
使用这样的方式有个很大的缺陷，那就是不要借属性之类的数据，因为它们是共享的，这是一个浅拷贝，还是因为指针的原因。不过要是继承方法，这种方式很方便。

还有一种方式跟上述类似，主要是封装了一层函数，用来返回对象。

###寄生组合继承

这样的方式主要解决的问题是调用两次父类的问题，避免额外的借来的属性或方法。想想看第一次Computer.call(this)，借来了this上的属性或方法，第二次Apple.prototype = new Computer()，又借来了this上的属性或方法，这里的初衷是想借原型，没办法这个是实例，所以该借的不该借的都借来了。那么要避免这样的问题，就要解决继承属性的继承属性，继承原型的继承原型，也不乱借。

	var extendPrototype = function(sub,supers){
		var F = function(){}
		F.prototype = supers.prototype;
		var _f = new F();
		_f.constructor = sub;
		sub.prototype = _f;
	}
	var Computer = function(name){
		this.name = name;
	}
	Computer.prototype.show = function(){
		alert(this.name);
	}
			
	var Apple = function(name){
		Computer.call(this,name);
	}
	extendPrototype(Apple,Computer);
			
	var apple = new Apple('apple');
	apple.show();

第一步把supers的原型赋值给F，第二步创建F的实例，第三步把_f实例的constructor属性修改成子类，第四步把_f实例赋值给子类的prototype。

这样的话就是不该借的也不会继承了

