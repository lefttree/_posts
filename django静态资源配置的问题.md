title: django静态资源配置的问题
date: 2014-11-14 20:16:20
tags: django
---

在初始化一个django项目时，我还遇见了一个比较头疼的问题，静态资源的配置，硬编码问题。其实，按照正常的部署来说，静态资源，比如（.css .js img）等，应该另启动一个服务器，去做静态资源的处理，这样是非常常见的，和语言无关，脱离了动态开发内容，特别的适合做内容分发。但是，考虑到前期，我想能在django中集合这样的资源类型，于是，碰到了比较纠结的问题。另外，在django初始化的项目中，竟然还不能在注释中打中文，是不是很疼，没关系，往下看，能解决这样的问题。

<!--more-->

其实，在django settings.py文件中，我们可以找到三个关于static的内容。STATIC_ROOT，STATIC_URL，STATICFILES_DIRS。于是，我在想，是不是就是配置这三项呢。

<a href="https://docs.djangoproject.com/en/1.5/ref/settings/">settings.py 官方给出的配置参数解释</a>


顾名思义，我想在我的项目中利用django内置的服务器来处理静态资源，最能给我合理的参数应该是STATICFILES_DIRS。

**This setting defines the additional locations the staticfiles app will traverse if the FileSystemFinder finder is enabled, e.g. if you use the collectstatic or findstatic management command or use the static file serving view.**

从官方的解释上来看，这个设置，可以通过我的管理命令集来启动这个视图，那不是意味着，它可以读取我的静态资源呢。实践下来，配置上，我项目的绝对路径，成功。

当然，我看django的官方配置文件中所描述的方式，有多种，而上述是一个最容易搞定的方式，反正是开发，何必纠结呢。不建议，在部署时，也使用django的静态资源服务，这会增加很多压力。合理的安排，是另外启用一个静态资源服务器，专门存储这些静态资源。


在处理好静态资源之后，我想给这里添加一个注释，并写上中文。于是，我发现，出现了一个编码问题。后来查询了python官方的一个PEP，了解到原来，如果我的不是utf-8或者其他支持中文的编码，直接写上中文，那是报一个大大的错误的。

<a href="http://legacy.python.org/dev/peps/">Python 官方关于编码的PEP</a>

在文件的头部写上#coding=utf-8即可，这一项2.7到3.x都是完美兼容的，如果现在学习python，建议最小选择从2.7.3开始，到3.x。2.7除了模块不一样名不一样，有很多内置函数，是继承到了3.x。当然如果，不考虑工作的话，我建议直接从python3.x开始学习。