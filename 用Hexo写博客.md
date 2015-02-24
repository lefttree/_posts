title: 用Hexo写博客
date: 2014-11-22 00:12:00
tags: nodejs
---

在看下面内容之前，请先确认是否已经安装nodejs，若无则先安装。

访问[Hexo](http://hexo.io/)，查看例子：

	npm install hexo -g
	hexo init blog
	cd blog
	npm install
	hexo server

hexo主要有如下几个命令来操作：

> hexo init  初始化一个hexo项目

> hexo server 监听4000端口启动一个服务器，可以预览，端口可以在_config.yml文件中修改

> hexo generate 输出静态文件，在public目录下

> hexo new 生成一个md文件，这个文件就是我们要写的博客文件了

> hexo clean 清除public目录以及缓存，其实这个缓存就是db.json文件

> hexo deploy 如果在_conig.yml文件中配置了这项，它会帮助我们发布到网络

一般大项的配置都是在顶层的_config.yml配置文件中配置，比如博客作者名，标题，时间格式，资源文件目录，静态资源文件目录，插件等等。而具体的博客配置，比如菜单之类的都在themes具体的主题_config.yml文件中配置。

<!--more-->

###具体的操作步骤

建立一个名为myblog的目录。

cd ~/myblog

输入：hexo init 初始化一下

然后npm install 安装所需要的模块

输入hexo server 可以先进行一下预览，看看是否已经安装成功

subl _config.yml 打开这个文件先修改一下配置，比如title，author之类的

**在配置文件中:后要空一格再输入**

接着在主题[themes](https://github.com/hexojs/hexo/wiki/Themes)中查看自己中意的主题，在插件[plugins](https://github.com/hexojs/hexo/wiki/Plugins)中查看自己需要的插件。具体的配置，主题要git clone 在themes目录中，然后修改_config.yml配置文件中的theme:[主题名]。而插件则可以用npm来安装，例如安装feed插件，npm install hexo-generator-feed --save，然后在_config.yml文件中任意位置配置：

	feed:
    	type: atom
    	path: atom.xml
    	limit: 20

基本上hexo渲染是可以用md来进行的，在配置好主题，插件之后，hexo new hello生成一个新的文章，然后开始你的写作之旅吧。

##开发主题

因为自己打算做一个关于订阅的项目，又不想自己写后台，于是根据hexo的特性，做了一部分修改，让主题不在局限于博客类型了，订阅主题可以使用[orange](https://github.com/lcepy/orange)，orange是完全参考的[http://weekly.ios-wiki.com/](http://weekly.ios-wiki.com/)而制作的基于hexo 2.4+的主题，它可以用于订阅类型的网站。

在开发自己的主题之前，可以先准备好静态文件。主题的开发主要依赖几个layout目录中的模版，除了布局模版，其他的可以根据自己的需求来修改。

访问[hexo docs](http://hexo.io/docs/)来获取具体的参考信息

> layout.ejs 是主题的一个基础框架模版

> index.ejs 是主题的首页

> post.ejs  是主题的内容页

> archive.ejs 是主题的分类页

这些相当于，手脚架，用户访问首页实际上访问的是layout.ejs + index.ejs 渲染出来的页面，而内容页则是layout.ejs + post.ejs 渲染出来的页面，当然，不仅仅只有这么四项，还有标签页等等，其他具体可以访问文档去参考。在这些模版中，可以使用<%- partial() %>来加载一个小模版，小模版可以放置在_partial目录下。

**全局变量**

> config 主要是可以获取到顶层的_config.yml中的配置项，比如想获取标题，<%= config.title%>

> theme 主要是可以获取主题_config.yml中的配置项，使用<%= theme.name%>

> site  在任何手脚架中都可以引用，site对象包含了posts（所有文章）pages（所有页面）tags（所有标签）categories（所有分类）可以each

**具体变量**

> page 个对象中包含了具体内容，这所有的内容。如果不是在post.ejs中使用，则可以each。若是，则是一篇具体文章的所有对象 <%= page.title%>

> post 包含所有文章的一个对象，可以each

**布局**

基础布局，也就是不带<%- body%>的布局，在这个布局中也可以自定义其他布局，比如link之类的,若找不到自定的布局的话，则会根据 Generator 的不同，使用相对应的布局代替。而带<%- body%>的layout布局，则是把page对象一一传递给最终所使用的模版中。

**注意事项**


{% raw %}
```
		Your code block with {{anything}} 
```
{% endraw %}

[#916](https://github.com/hexojs/hexo/issues/916)

**额外的学习**

主要是看看ejs模版如何使用，亲，玩转自己的主题去吧。