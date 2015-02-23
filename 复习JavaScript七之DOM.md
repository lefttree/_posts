title: 复习JavaScript七之DOM
date: 2015-01-28 19:51:01
tags: node&js
---
DOM是针对HTML和XML文档的一个API，主要是使用JavaScript来进行编程操作HTML和XML文档。其他语言如果实现了DOM标准，理论上也是可以使用这个API的，这里仅仅讨论JavaScript的应用。

**理解层级结构与关系**

在浏览器中比如HTML页面是由很多有层次结构的标签组成的，而为这些标签提供查询，添加，删除等等方法主要就是DOM在提供支持。

（页面又称为文档）文档中所有的节点之间都存在这样或那样的关系，比如下面一个经典的HTML：

	<html>
		<head></head>
		<body></body>
	</html>

一个标签又可以称为一个元素，head和body那就是兄弟关系，它们都来自一个父系html，又可以说html的子元素是head和body，可能这样描述还不太明显，这样就用原生Js操作DOM来的方式来看看层级结构。

	var html = document.getElementsByTagName('html')[0];

先通过getElementsByTagName获取html根元素的节点，每一个元素都有一个childNodes集合和一个parentNode分别代表子节点集合和父节点，**如果不存在，则都是null，如果是集合不存在，则是一个[]**。

> html的childNodes //[head,body] html的parentNode // document

每一个元素也都有一个firstChild和lastChild来分别代表第一个子元素和最后一个子元素

每一个元素也都有一个nextSibling和previousSibling分别代表前面一个元素和后面一个元素，以当前自己为参照物。

从这样可以看出来，它就像族谱一样对元素的关系进行了定义，通过理解这些层级关系，利用DOM提供的API可以很顺利的进行操作。

###操作DOM

**常见的获取方式**

* document.getElementById （通过ID来获取到节点）
* document.getElementsByTagName （通过节点标签来获取）
* document.querySelector  
* document.querySelectorAll

后面两个属于HTML5提供的新API，在移动端会用的比较多，前者是获取单个，后者获取集合。

**常见添加，删除**

* appendChild
* insterBefore
* replaceChild
* removeChild

appendChild主要是向childNodes集合的末尾添加一条元素，insterBefore可以用来插入特定位置，两个参数，要插入的节点和作为参照的节点，更新成功后插入的节点会在参照节点之前，也就是参照节点的previousSibling。replaceChild和insterBefore有些类似，两个参数，要插入的节点和参照节点，更新成功后，要插入的节点会替换参照节点，removeChild就比较好理解了，删除一个节点，这四个方法都有返回值。

**常见元素属性**

一般来说，如果var doc = document.getElementById('doc');doc.id = 'xx';这样的方式也是可以更新或者获取到元素的属性的，不过不推荐这么使用，要获取元素的属性，DOM API也提供了三个方法来使用。

* getAttribute
* setAttribute
* removeAttribute

getAttribute可以获取元素的属性，setAttribute可以对元素的属性进行设置，如果属性名不存在，则创建该属性。removeAttribute则是完全删除此属性。

还有一个属性attributes，主要是获取元素属性集合，这个不是很常用，主要是在遍历元素属性时会使用到，它是一个集合。

**常见创建元素或文本**

一般情况下创建元素都会使用字符串的形式，innerHTML进去。不过，某些情况下，会用到createElement来创建一个元素，如果用到它，那么创建的文本也必须使用createTextNode了。

对于文本节点，注释节点等开发真的很少用，可以当一个子类大概了解即可。

> 关于模式的讨论，主要可以用document.compatMode来判断，如果是CSS1Compat就是标准模式，移动端不会出现这样的情况，IE上可能有别的模式，模式主要是影响到CSS布局上，Js影响非常少。

> 在移动端上滚动是一个比较要处理的问题，一般来说会使用scrollIntoView，scrollIntoViewIfNeeded，scrollByLines，scrollByPages，这四个方法safari chrome都有实现，意味着在iOS和安卓平台都是良好的。

* scrollByPages 将元素的内容滚动到指定的页面高度，具体的高度是由元素的高度来决定的。
* scrollByLines 将元素的内容滚动到知道的行数高度，参数可正可负。
* scrollIntoViewIfNeeded，当元素在视窗（viewport）不可见，会滚动容器元素或者浏览器窗口让其可见。如果是可见的，这个方法不起任何作用。如果参数为true，可能是垂直居中的可见。
* scrollIntoView 滚动容器元素或者浏览器窗口，让元素可见。

**一些小技巧**

每一个元素都存在一个contains方法，用来检测传入的节点是不是当前节点的子节点，火狐对于的方法名叫compareDocumentPosition。

如果要获取一个文本节点可以使用innerText（纯文本）来获取字符串，如果要获取所有的包括标签的字符串可以使用innerHTML。它们还有一种outer系列对应的方法，主要的区别是前者（outerText）会替换节点，后者(outerHTML)会修改调用它的元素，一般基本没人使用。它们可以获取，也可以通过赋值来设置新的节点。

###DOM2和DOM3

对于这两级在DOM中基本上IE没啥支持，或者说支持的非常少，像style对象，CSS的一些对象外。

这里最大的变化是增加了对XML命名空间的支持，元素样式的访问，节点的遍历以及range。当然目前来看，节点的遍历，range，XML命名空间在开发中使用的非常少，可以当资料来阅读，了解有这么回事，用到的时候再查询。而元素样式的访问，这个在开发中普遍使用的较多，因为在没法使用css3动画的浏览器中，可以通过改变样式来到达动画的目的。

	var doc = document.getElementById('doc');
	doc.style.width = '100px';

对于iframe的访问这里增加了一个contentDocument对象来进行引用，还有节点的比较，isSameNode和isEqualNode，这两个的区别在于，前者是否引用的同一个节点对象，后者是指两个节点是否是相同的类型。不过，它们使用的也不多，了解就好。

**元素的大小**

这个部分需要理解，因为关乎到元素在浏览器上的位置显示，跟动画有关系，四个属性。

* offsetWidth 元素在水平方向占用的空间大小
* offsetHeight 元素在垂直方向占用的空间大小
* offsetLeft 元素的左外边框到内边框的距离
* offsetTop 元素的上外边框到内边框的距离

**滚动大小**

这个在视察滚动或者处理滚动条的时候用的上，也是四个属性

* scrollHeight 在没有滚动的情况下，元素的总高度
* scrollWidth 在没有滚动的情况下，元素的总宽度
* scrollLeft 被隐藏在内容区域左侧的像素度
* scrollTop 被隐藏在内容区域上侧的像素度

> 下面这些IE全部不支持，range支持一种叫做文本范围的东西


**元素遍历**

关于遍历其实有两个方法可用createNodeIterator和createTreeWalker，不过这些在开发中几乎不会使用到，谁没事去遍历节点完呢。

**关于range**

这个也是非常少会使用到，除非是做那种编辑器应用或者在线编辑器等等，不过使用它可以更精准的控制的DOM，主要是使用createRange方法。















