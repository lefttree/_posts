title: 复习JavaScript六之事件
date: 2015-01-27 18:08:04
tags: JavaScript
---

> IE浏览器的事件不是复习的重点

事件是JavaScript与HTML进行交互的一个纽带，理解事件可以更好的处理Web应用程序，现在的浏览器中主要支持两种事件流：

* 事件冒泡
* 事件捕获
* DOM事件流

事件冒泡则是指事件开始时由具体的元素接收，然后逐级向上传播。比如：

	<html>
		<head></head>
		<body>
			<div>
				<p></p>
			</div>
		</body>
	</html>

给p标签监听一个事件，它的流向是p,div,body,html,document，其实细心看来这种流的走向会存在一个问题，给div也监听一个事件，当用户点击P的时候是会触发两次的，好在event对象中有可以阻止事件冒泡的方法。

事件捕获则是指事件由最上级接收，逐级向下传播到具体的元素上，了解了冒泡之后这个就非常好理解了，正是一个相反的步骤。

而DOM事件流又正好是冒泡与捕获的结合体，它分为三个阶段：事件捕获，目标事件，事件冒泡，如果在纸上画出来，它的走向就是一个圆形。

> 对于事件处理程序，写在HTML标签中的，另外一种是直接写一个function的，比如doc.onclick = function(){}，一般来说这些浏览器支持，但是基本上不会使用了。因为前者是跟HTML耦合的，不利代码维护，而且虽然HTML加载了但是Js文件还未加载，用户点击后，是直接报错的。后者虽然也可以删除，比如doc.onclick = null，对于对代码有强迫症的同学，基本上不会使用到它。

那么，我们该怎么给一个元素添加上事件处理程序呢？

###DOM2级事件处理程序

* addEventLister
* removeEventLister

所有的DOM节点都具备这两个方法，它接收三个参数：

* 要处理的事件名称，比如click（这里跟上述两个以及IE注册事件都不同，不需要on）
* 需要事件进行处理的函数
* 一个布尔值，表示（true，在捕获阶段调用事件处理函数）（false，在冒泡阶段调用事件处理函数）

>一般情况下第三个参数都填false

IE浏览器对应的两个方法，attachEvent，detachEvent，它们只有冒泡，事件名要加上on。

###事件对象

在注册完事件处理程序后，事件的一个比较重要的对象必须要理解，**event事件对象**。

一般来说，这个对象中包含着所有与当前元素所监听的事件有关的信息，比如元素监听的事件类型，元素本身等等。

**比较重要的属性和方法（只读）**

* currentTarget 真正监听事件的那个元素
* target 事件的目标元素
* type 事件的类型
* perventDefault()  取消事件的默认行为
* stopPropagation()  取消事件的捕获或者冒泡
* bubbles 事件是否冒泡
* eventPhase 事件处理程序的三个阶段，1捕获2处于目标3冒泡

**比较重要的属性和方法（读写）**

* clientX 鼠标在窗口中的水平位置
* clientY 鼠标在窗口中的垂直位置

###事件类型

> PC端主要是针对鼠标，移动端则是触摸，手势相关的处理

如果在PC端上发生一次click事件，实际上它是发生了三次事件，mousedown当鼠标按下的时候，mouseup当用户放开的时候，click两个加起来就发生了一次click事件。相对于移动，PC上的鼠标事件非常的丰富，例如mouseover当鼠标首次移入一个元素边界时触发，mouseout当鼠标移出元素时触发，这个移出，到子元素上也会触发这个事件，mousemove当鼠标在元素内移动时重复触发。

总体来说对于文档加载，表单控件，窗口大小改变等事件，比如获取焦点，在失去或者获取焦点是值改变等移动上都是一样的，focus（获得焦点）blur（失去焦点）。

在做一些视差滚动的效果时scroll事件是非常好用，移动上在css中提供了一个类似的属性。

唯一的区别是移动端上没有键盘事件。

###移动事件

* touchstart 当手指触摸到屏幕时触发
* touchmove 当手指在屏幕上连续滑动时触发
* touchend 当手指从屏幕上移开时触发
* touchcancel 当系统停止跟踪触摸时触发（这个事件没有确定的触发时间）

它们都是冒泡的，也可以取消

**三个跟踪触摸事件的属性**

* touches 当前跟踪触摸操作的touch数组，在touchend事件中为空
* targetTouchs 特定事件目标的touch数组
* ChangedTouches 上次触摸时发生了什么改变的touch数组

**移动event事件对象**

PC上存在的，在移动上也存在，描述上有差异，比如

* target 触摸的DOM节点目标
* pageX 触摸目标在页面中的X坐标
* pageY 触摸目标在页面中的Y坐标

**一些手势**

* gesturestart 当一个手指按在屏幕上另外一个手指又触摸屏幕时触发
* gesturechange 依赖前者当其中的一个手指发生改变时触发
* gestureend 当任何一个手指离开时触发

**移动手势干货三部曲**

[对于Touch的处理](http://www.cnblogs.com/pifoo/archive/2011/05/23/webkit-touch-event-1.html)

[处理简单手势](http://www.cnblogs.com/pifoo/archive/2011/05/22/webkit-touch-event-2.html)

[处理复杂手势](http://www.cnblogs.com/pifoo/archive/2011/05/22/webkit-touch-event-3.html)