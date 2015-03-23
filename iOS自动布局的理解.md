title: iOS自动布局的理解
date: 2015-03-07 11:47:44
tags: iOS
---
一般情况下iOS的自动布局可以在Xcode中利用图形化的工具来操作完成，当然也可以使用代码的方式来完成，这些工作主要是对视图定义一些规则和约束，在容器变化的时候，视图可以遵守这些约束。

实现约束的三种方式：

* 比如拖动一个Label在storyboard中，会出现蓝色的辅助线，放定好位置时，在下方的小按钮中点击Reset to Suggested Constraints，系统会帮助我们选择最佳的自动布局方式
* 拖动一个Label到storyboard中，依然是点击下方小按钮，有add，手动添加。
* 拖动一个Label到storyboard中按住control键，拖动到想放的位置上，这里有一个前提，是有一个参照视图，放开的时候会弹出一个小框来让你选择约束

比如要查看效果，可以右边的属性栏中选择Orientation中的Landscape，就变成了横屏。如果想删除约束，可以在左侧的文档大纲中进行删除，假设一个视图没有约束，也可以在文档大纲中来add约束。

如果想要查看一个约束，可以在Show the size inspector中查看constraints。