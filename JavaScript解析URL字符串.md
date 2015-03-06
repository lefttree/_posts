title: JavaScript解析URL字符串
date: 2015-03-06 14:38:32
tags: JavaScript
---

说到解析URL思路应该有多种多样的，比如一点一点的分解，先indexOf('?')判断是否有参数，完了之后split('?')一下，后面的参数继续split('&')成一个数组然后循环再split('=')组装成一个一个key和vaule的键值对对象。前面那一部分也可以继续分解，把端口，协议，路径等等分解出来。或者用正则表达式，一个一个的分解。除了上述的思路，其实还有一个更简单的办法，利用a标签。

	var parseURL = function(url){
		var temp =  document.createElement('a');
		temp.href = url;
		var result = {
			"port":temp.port,
			"protocol":temp.protocol.replace(':',''),
			"hash":temp.hash.replace('#',''),
			"host":temp.host,
			"href":temp.href,
			"hostname":temp.hostname,
			"pathname":temp.pathname,
			"search":temp.search,
			"query":{}
		}
		var seg = result.search.replace(/^\?/,'').split('&'),
			leng = seg.length,
			i = 0,
			target;
		for(;i<leng;i++){
			if(!seg[i])continue;
			target = seg[i].split('=');
			result.query[target[0]] = target[1];
		}
		temp = null;
		return result;
	}
	var url = 'https://github.com:8989?key=value&hub=123ASFDGGR#github';
	parseURL(url);

最后使用完了，把对象的引用设置为null，等待内存回收。

说到URL就不得不提一下编码和解码的函数，Js里提供了三组，其中最常用的一组是encodeURIComponent和decodeURIComponent。

通过对三个函数的分析，我们可以知道：escape()除了 ASCII 字母、数字和特定的符号外，对传进来的字符串全部进行转义编码，因此如果想对URL编码，最好不要使用此方法。而encodeURI() 用于编码整个URI,因为URI中的合法字符都不会被编码转换。encodeURIComponent方法在编码单个URIComponent（指请求参数）应当是最常用的，它可以讲参数中的中文、特殊字符进行转义，而不会影响整个URL。


