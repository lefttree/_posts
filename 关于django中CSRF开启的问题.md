title: 关于django中CSRF开启的问题
date: 2014-11-14 20:33:12
tags: django
---

##django 处理CSRF机制保护下的POST表单提交

昨天晚上使用django做一个POST表单提交的功能，发现出现了一个CSRF verification failed. Request aborted错误。于是，在网上找了很久，也没有什么好的办法可以解决它。我发现，大部分人的博客都不靠谱，Ctrl+C Ctrl+V的复制，坑了我不少时间，于是我去了django的官网，查询CSRF的问题，最后解决了。其实在解决这个问题之前，我们先要了解一下，CSRF到底是什么东东，为什么django提交一个POST表单这么困难呢。

<a href="http://baike.baidu.com/link?url=OnI0sPQAHpQHhtxWSXvQFhN3kJWI3CU_rP3Fo7MqnPY3rTdhUPpuca-3lzJ651LdMI7ouoLvdnnihhojFyKtva">CSRF 是什么</a>

<a href="https://docs.djangoproject.com/en/1.5/ref/contrib/csrf/">django document CSRF</a>

原来CSRF是为了防止跨站请求伪造用的，当我明白了这个解释之后，我开始找寻django document上如何开启CSRF。

**Add the middleware 'django.middleware.csrf.CsrfViewMiddleware' to your list of middleware classes, MIDDLEWARE_CLASSES. (It should come before any view middleware that assume that CSRF attacks have been dealt with.)**

那我在想，如果我把django.middleware.csrf.CsrfViewMiddleware注释掉，是不是就可以不开启CSRF来提交POST表单，人们常说实践是检验真理的唯一标准，试验一下，原来是可以的。但是这并没有解决，我想解决的问题，于是我又把这个中间件，重新写在了MIDDLEWARE_CLASSES上。

从官方的解释上来看，只要在MIDDLEWARE_CLASSES上加django.middleware.csrf.CsrfViewMiddleware就开启了CSRF机制。

现在我需要在模板中加入一个tag ｛%csrf_token%｝，来存放一个隐藏的token input。

**In the corresponding view functions, ensure that the 'django.core.context_processors.csrf' context processor is being used. Usually, this can be done in one of two ways:**

从这里的解释来看，我还需要django.core.context_processors.csrf，于是我导入这个模块from django.core.context_processors import csrf，运行一下貌似，还不行，接着往下看。我发现django的解释，这里可以用两种方式来处理，最后响应的response，而且django还说了，POST表单提交不要跨链接。

<!--more-->
**Use RequestContext, which always uses 'django.core.context_processors.csrf' (no matter what your TEMPLATE_CONTEXT_PROCESSORS setting). If you are using generic views or contrib apps, you are covered already, since these apps use RequestContext throughout.**

方式一，是使用RequestContext来代替你模板的Context，并且你的apps必须都要使用它，而不是Context。

**Manually import and use the processor to generate the CSRF token and add it to the template context. e.g.**

方式二，手动的导入这个模块并调用此函数生成的token，并把它传入你模板的上下文中。

我使用的是方式二，在这里也许刚刚接触django的朋友会卡在这里，因为没法明白，怎么传入这个上下文。这里可以使用一个通用的方式去解决这个问题。请原谅我，我将脱离CSRF的内容，帮助你解决一下模板设置的问题，解决它才能解决它。回到django-admin.py startproject xxx初始化的项目中，打开settings.py文件，它是整个django项目的配置文件。找到TEMPLATE_DIRS配置上你模板目录的绝对路径，也可以是os.path生成出来的路径。然后通过from django.shortcuts import render_to_response来导入render_to_response。

	xxx.html结构
     
    <html>
    	<head></head>
		<body>
			<h1>{{we}}</h1>
			<form action="/" method="post">
 				｛%csrf_token%｝
				<input type="email" name="address">
				<button type="submit" class="btn btn-default">send</button>
			</form>
		</body>
	</html>



	views.py ##视图文件

    from django.template import Context
	from django.core.context_processors import csrf
	from django.shortcuts import render_to_response
	import models

    def index(request):
		dicts = {'we':'i send string'}
    	token = {}
		token.update(csrf(request))
		if request.method == 'POST':
        	address = request.POST.get('address')
			## 可以用address来做其他验证或者其他处理程序
			## 此处省略其他处理函数
		data = Context(dicts)
		return render_to_response('xxx.html',c,data)
		pass

	urls.py ##路由配置文件，我的views是一个app所以需要导入xxx.views
	import xxx.views
	urlpatterns = patterns('',
	    # Examples:
	    # url(r'^$', 'atomcoffee.views.home', name='home'),
	    # url(r'^atomcoffee/', include('atomcoffee.foo.urls')),
	    url(r'^$',xxx.views.index),
	    # Uncomment the admin/doc line below to enable admin documentation:
	    # url(r'^admin/doc/', include('django.contrib.admindocs.urls')),
	
	    # Uncomment the next line to enable the admin:
	    # url(r'^admin/', include(admin.site.urls)),
	)

大家可以尝试运行一下这个文件，你会发现，原来开启CSRF机制后的POST表单提交，成功了。