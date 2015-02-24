title: 携程邮轮开发部django分享
date: 2014-11-14 20:20:19
tags: django
---

使用django快速开发一个web网站

分享内容（拟）

1. 介绍django的历史
2. 安装以及配置django项目
3. 介绍django对请求的回应机制
4. 讲解django URL的配置
5. 讲解django模型的使用
6. 讲解django view template 的使用
7. 通过一个例子讲解如何快速的实现一个简单的网站（注册，登录，验证用户状态）
8. 分享学习Python django的资料，以及说说一些django的web工具

<!--more-->

##django的历史

django是一个开放源代码的Web应用框架，由Python写成。采用了MVC的软件设计模式，即模型M，视图V和控制器C。它最初是被开发来用于管理劳伦斯出版集团旗下的一些以新闻内容为主的网站的，即是CMS（内容管理系统）软件。并于2005年7月在BSD许可证下发布。这套框架是以比利时的吉普赛爵士吉他手django reinhardt来命名的。

**人生苦短，我用Python。**

##安装以及配置django项目

安装方式一：

下载[django](https://github.com/django/django)源代码到本地，进入这个目录，执行命令：python install setup.py

安装方式二：

下载[pip](https://pip.pypa.io/en/latest/)模块管理器到本地，执行命令：python install get-pip.py

然后使用pip进行安装，pip install django。

使用pip安装，可以很方便的删除，选择需要安装的django版本，例如：pip install django==1.5.2安装1.5.2版本的django。

个人推荐，使用方式二。

在安装完成好django之后，可以在python shell 交互控制台中测试django是否安装成功。

```python

	import django
	
	print(django.get_version()) #1.5.2

```

django-admin.py是django提供了一个强大的shell命令行工具，比如建立项目，建立项目内应用，启动开发服务器，测试模型SQL语句，同步模型到数据库等等，用好django出了需要具备python标准库，语法知识，以及django定义的类，组件之外，还需要学习好它的shell命令行工具，admin管理系统等，因为它们会让开发一个web应用，变得非常简单与快速。

新建立一个项目：cruise

	某目录内 ~/ django.admin.py startproject cruise

cruise项目目录：

	cruise (dir)
		wsgi.py
		setting.py
		urls.py
    manage.py


建立一个应用：login

	cruise目录内 ~/ python manage.py startapp login

login应用目录：

	login（dir）
		views.py
		models.py
		test.py

**概念** django项目中，各种服务类的功能，可以分离成一个个应用，而外层才是项目的配置文件。

wsgi.py 是一个符合wsgi标准的文件，由django实现，[WSGI资料可查看](http://wsgi.readthedocs.org/en/latest/)

setting.py 为django项目的配置文件，django实现的组件，以及开发时模板，静态资源等等都在此配置。

urls.py 可以理解为django为view实现的路由系统。

##介绍django对请求的回应机制（3）

当服务器接收到一个HTTP请求后，一个服务器特定的handler会创建HTTpRequest并传递给下一个处理程序，这个处理程序，会处理URLConf，并调用相应的view，把REQUEST传递给view 处理程序。

view处理程序，当处理完成之后，进行响应给客户端。另外django的中间件会，增强这些响应的作用。

##django URL的配置（4）

首先需要先导入django.conf.urls.defaults下的所有模块，它是django URLConf的基本构造，包含了一个patterns函数，调用patterns函数并将返回结果保存到 urlpatterns 变量。实际在建立项目时，django生成的文件中，已经帮助我们做好了这件事情。

它所需要的参数是一个元组，用来映射视图与URL的关系。

**额外：Python路径搜索规则，建议查看资料。**

```python

	#coding=utf-8
	from django.conf.urls import patterns, include, url
	# Uncomment the next two lines to enable the admin:
	# from django.contrib import admin
	# admin.autodiscover()
	import login.views
	import home.views
	#在django中urls.py的作用有点类似于C，实际上它就是一个分发程序，帮助我们对每一项URL进行分发到具体的视图程序中。
	urlpatterns = patterns('',
	    # Examples:
	    # url(r'^$', 'cruise.views.home', name='home'),
	    # url(r'^cruise/', include('cruise.foo.urls')),
	    url(r'^login/$',login.views.sign_in),
	    url(r'^logout/$',login.views.sign_out),
	    url(r'^register/$',login.views.register),
	    url(r'^$',home.views.mypage),
	    # Uncomment the admin/doc line below to enable admin documentation:
	    # url(r'^admin/doc/', include('django.contrib.admindocs.urls')),
	    #URL配置例子
	    # Uncomment the next line to enable the admin:
	    # url(r'^admin/', include(admin.site.urls)),
	)

```
**URL正则表：**

>符号	匹配
>. (dot)	任意单一字符
>\d	任意一位数字
>[A-Z]	A 到 Z中任意一个字符（大写）
>[a-z]	a 到 z中任意一个字符（小写）
>[A-Za-z]	a 到 z中任意一个字符（不区分大小写）
>+	匹配一个或更多 (例如, \d+ 匹配一个或 多个数字字符)
>[^/]+	一个或多个不为‘/’的字符
>*	零个或一个之前的表达式（例如：\d? 匹配零个或一个数字）
>*	匹配0个或更多 (例如, \d* 匹配0个 或更多数字字符)
>{1,3}	介于一个和三个（包含）之前的表达式（例如，\d{1,3}匹配一个或两个或三个数字）

###简单思路

导入视图模块

```python

	import login.views
	import home.views

```
django URLConf 的配置，有多种多样的形式，这里主要是介绍一下简单的URL规则，在Python风格中URL应该是双斜杠//，例如：127.0.0.1/home/或者127.0.0.1/home/1/ 。

一般情况下一个URL是对应一个视图的，例如127.0.01/home/可能对于的就是视图home.views.home。而在URL中也可以动态，比如情况是书籍的详细情况，一本书有一个唯一的书籍号ID，而书籍号ID就可以描述为：127.0.0.1/book/1/，这是ID为1的书籍，又如127.0.0.1/book/2/，这是ID为2的书籍。

如何实现动态URL？

之前，有看过URL正则表达式，django URLConf是支持正则表达式，来描述URL的。它会成第二个参数，传递给视图处理程序。例如，我想获取书籍号ID，来显示不同本书籍，那么URL：^book/(\d+)/$，而在视图处理程序中第二个参数，就是GET请求时，获取的book/(\d+)/，这样的数字ID。

总之URLConf可以通过这些组合来映射相应的视图处理程序，来满足业务需求。

##django模型的使用（5）

在使用模型之前，需要先配置数据库，django支持的数据库类型表：

`` postgresql``	PostgreSQL	psycopg 1.x版， http://www.djangoproject.com/r/python-pgsql/1/。

postgresql_psycopg2	PostgreSQL	psycopg 2.x版， http://www.djangoproject.com/r/python-pgsql/。

mysql	MySQL	MySQLdb , http://www.djangoproject.com/r/python-mysql/.

sqlite3	SQLite	如果使用Python 2.5+则不需要适配器。 否则就使用 pysqlite ， http://www.djangoproject.com/r/python-sqlite/。

oracle	Oracle	cx_Oracle , http://www.djangoproject.com/r/python-oracle/.

主要为：mysql oracle sqlite等等。

打开setting.py文件，在起始位置出，默认情况下，这些都是无配置的，需要手动配置好。

```python
	# 配置数据库模型
	DATABASES = {
	    'default': {
	        'ENGINE': mydb.DB_ENGINE, # Add 'postgresql_psycopg2', 'mysql', 'sqlite3' or 'oracle'.
	        'NAME': mydb.DB_NAME,                      # Or path to database file if using sqlite3.
	        # The following settings are not used with sqlite3:
	        'USER': mydb.DB_USER,
	        'PASSWORD': mydb.DB_PASSWORD,
	        'HOST': mydb.DB_HOST,                      # Empty for localhost through domain sockets or '127.0.0.1' for localhost through TCP.
	        'PORT': mydb.DB_PORT,                      # Set to empty string for default.
	    }
	}
```

**技巧**

可以使用python manage.py shell 进入一个交互环境，在这里可以测试一下增删改查等等。

django模型的使用是django框架学习比重非常大的一个地方，它实现了一套属于django自己的方式来操作数据库，包括增删改查，以及字段类型的定义，查询的技巧等等方式。

django中一个模型对应的是一张表，需要继承django.db中models类。

定义一个模型：

```python
	from django.db import models
	class x(models.Model):
	    u_name = models.CharField(max_length=45)
	    u_pass = models.CharField(max_length=45)
	    u_session = models.CharField(max_length=45)
		u_status = models.IntegerField(max_length=1,default=0)
    	pass
```
CharField对应的是数据库中的varchar类型，而IntegerField对应的是int类型，自增主键，django为每一个模型定义了一个主键id。

**增**

要往表中插入一条数据，SQL的语句是INSERT INTO。而django则是实例一下定义的模型类，比如这里定义的x。

```python
	y = x(u_name='123',u_pass='a123',u_session='123')
	y.save()

```

最后调用一下save方法，来真正插入一条新的记录。

**查**

	y = x.objects.filter(u_name='123')

	y = y[0]

	print(y.u_name)

**改**

	y = x.objects.filter(u_name='123')

	y = y[0]

	y.u_name = 456

	y.save()

**删**
	
	y = x.objects.filter(u_name='123')

	y = y[0]

	y.delete()

**其他，比如过滤，排序，更新多条数据，限制返回数据，连锁查询，获取单个对象等等，这些需要学习时间**

##django view template 的使用（6）

也许我们认为django是一个MTV框架，更加适合。M：模型 T：模板 V：视图。C的管理，由django内部去实现了。

在视图中文件中，需要导入视图相关的django组件，比如django.http 中的各类响应类，django.template中的与模板相关的类。

	from django.http import HttpResponse
	from django.template import Context,Template
	def hello(request):
		return HttpResonse('<h1>Hello</h1>')
		pass

这里写了一个hello视图函数，并在URLConf中配置好这个视图函数，即可在浏览器中查看这个页面了。

**视图函数中所使用的各种类，需要一定的时间学习。**

它不是最重要的，T才最重要的。

**简单基础**

django模板系统，对于逻辑的操作，需要用百分号，必须有结束语句。而赋值则是两个大括号。django的模板系统，是可以切换的。如果觉得原生自带的模板系统不够好用，可以在setting文件中重新配置要使用的模板系统。


**简单例子**

```python
	from django.http import HttpResponse
	from django.template import Context,Template
	def hello(request):
		t_html = Template('''
			<h1>{{page}}</h1>
			{%if wow%}
				<h1>wow</h1>
			{%endif%}
		''')
		c = Context({"wow":True,"page":"Hello"})
		t_html = t_html.render(c)
		return HttpResponse(t_html)
		pass

```
**扩展：模板系统也是一个学习重点，中间有很多技巧性的问题，比如：导入，同一个模板多个上下文，过滤器等等**

##扩展部分

1. 通过一个例子讲解如何快速的实现一个简单的网站（注册，登录，验证用户状态）
2. 简单django中几个常用的组件
3. 分享学习Python django的资料

django提供了N多的web工具，例如：

	认证
	缓存
	日志
	发送电子邮件
	聚合供稿(RSS/Atom)
	评论, 评论审核 和 自定义评论
	分页
	消息框架
	序列化
	Sessions
	站点地图
	静态文件管理
	数据验证

django是一个大而全的框架，它提供了很多很多基本上包括了web开发中所需要的功能，学习它需要大量的时间。

学习django的基础，是学习好Python。

**人生苦短 我用Python**

[Python 标准库](https://docs.python.org/3/library/index.html) 

[Python2x与Python3x对照表](http://docs.pythonsprints.com/python3_porting/py-porting.html)

[Django API](https://docs.djangoproject.com/en/1.6/)

[一本django1.5电子书](http://djangobook.py3k.cn/2.0/)

[简单的django学习](http://uliweb.clkg.org/tutorial/read/20)