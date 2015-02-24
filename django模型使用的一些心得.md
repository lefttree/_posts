title: django模型使用的一些心得
date: 2014-11-14 20:30:31
tags: django
---

近很一直在学习django，因为我需要使用它为自己的项目写一个前台页面和一个后台服务。它是一个关于咖啡的邮件订阅社区，既然有服务，自然需要数据库层的支持。这是一个很好的开始，学习django让我了解到python开发web应用真的非常快速，但是也有一个很明显的缺点。那就是国内，对django社区的支持度非常不好，这或许跟国内python的有关，不过也不要紧，学习就是这样，要啃难而上。

说说在django中模型，对应的数据库层，也就是意味着django帮助我们完成了对数据库的访问，操作，通过它的模型类。

>django-admin.py startapp sendcloud

通过上述的命令，我们可以完成一个app模块，这个模块中带有views.py models.py test.py，而models.py文件中，就是我们完成模型的一个最主要的文件。

>from django.db import models

模型类，都需要继承自django.db.models类。通过它与manage.py来完成初始化等等动作。关于manage.py的命令，未来有时间，我会写写关于它的心得。

	class WeeklySyncList(models.Model):
		#do something
		pass

一个模型类对于数据库层中的一个表，在我们定义好模型类之后，可以在命令行中输入：

>python manage.py sqlall sendcloud

来完成对创建语法的查看以及错误信息的查看，准确没有错误之后，可以输入：

>python manage.py syncdb

来初始数据库，django会帮助我们完成数据库层面的操作。

<!--more-->

##说说我的应用中使用到的东西

我的应用是一个关于咖啡资料的邮件订阅社区，前台页面主要是输入email完成订阅，激活订阅，退订，历史期刊的分页查看，最新一期查看。这里，我使用了三个表来描述它，（这个数据库中还有另外的表，在描述后台服务，这里仅仅用前台来说说我学习的过程。）用户订阅表，用户退订表，期刊表。在语言层面，就需要三个模型类，来描述它了。

另外：这个应用，已经开源在github上，大家通过它可以访问到[点击这里](https://github.com/lcepy/AtomCoffee-Client)

我用到的字段类型，主要是三个字段varchar date int，主要是用于订阅的email，订阅时间，激活时间，是否激活的状态。

在django中，因为我们不是使用原始查询，所以还需要在模型类中定义相应的字段，这一部分需要学习，对应的就是数据库中的字段类型，但是django有它自己的描述。

**用户订阅时插入数据库**

	models.WeeklyActivation(activation_email=address,activation_key=_ac_sha_key)

用户条件的一条email，会经过在后台的验证，验证成功之后就插入一条新的纪录。从代码上来看，基本上实例化一个类。

**查询数据库**

在验证email之后，我会根据提交的信息，先去数据库中查询一下是否有这个email地址，另外它是否是激活的状态。

	models.WeeklyActivation.objects.filter(activation_email=address)

如果存在，并且已经激活，自然就不用插入一条新的纪录了。如果订阅时间，超过了两个小时，系统就需要发送一封信的订阅激活邮件了。

在django模型类中，objects叫做管理器，通过它，可以完成一些不同的操作，比如查询，过滤，等等。

**更新数据**

当用户收到一封激活email时，用户点击URL过来，需要更新一下激活状态，初始化状态都是0。

	week = models.WeeklyActivation.objects.filter(activation_key=offset)

根据用户点击过来的URL获取到激活key，根据激活KEY查询一条记录，如果存在则通过week完成其他的操作。

这里还有地方要注意，查询返回的结果是一个list，要week = week[0]，然后再来操作，更新完成之后不要忘记调用week.save()方法，不然更新不会成功。

**原生查询**

在查看最新一期期刊的这个地方，我纠结了大概一个小时，因为我不清楚django模型，是怎样完成数据库函数的调用的，比如一个表中，我只需要某个列的最大值，这里如果是原生SQL，我一般都会用MAX函数来完成。

在没有找到django关于这方面的信息之前，我用了原始查询，django支持这个。

	模型类.raw('SELECT id,MAX(sync_number) AS max_number FROM sendcloud_weeklysynclist')

另外这里也需要关注一个问题，如果要原始查询，必须要带上主键，不然django会抛一个错误出来。