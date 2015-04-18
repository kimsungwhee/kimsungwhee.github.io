---
title: Origami Facebook 原型设计工具 学习笔记
author: admin
layout: post
permalink: /origami-facebook-%e5%8e%9f%e5%9e%8b%e8%ae%be%e8%ae%a1%e5%b7%a5%e5%85%b7-%e5%ad%a6%e4%b9%a0%e7%ac%94%e8%ae%b0%ef%bc%881%ef%bc%89/
categories:
  - UI
tags:
  - Facebook
  - Origami
  - 原型设计
---
前几天Facebook开源了一个项目Origami，看了报道才知道原来Facebook的Paper是通过Origami由美工来设计UI并且制作原型的。

Origami的最大的特色就是美工不需要会编程就能简单的制作UI原型，这点真的很方便，以前在公司制作的原型的时候，美工往往就是直接给我一个PSD，甚至连切图都不给我切，最后还得我自己切。郁闷。这回美工估计就不能偷懒了。呵呵。不过话说回来，对于设计来说还真就得由美工来制作原型比较好，因为光有图片，程序员很难知道他到底是想要什么养的效果，特别是关于动画方面的。

官方下载Origami

http://facebook.github.io/origami/

今天就简单的来分析一下，官方提供的一个例子（Alpah and Scale）。

大概的效果如下：

[<img class="alignnone size-medium wp-image-112" alt="QQ20140220-1" src="http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-1-144x300.png" width="144" height="300" />][1][<img class="alignnone size-medium wp-image-113" alt="QQ20140220-2" src="http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-2-144x300.png" width="144" height="300" />][2]

&nbsp;

<!--more-->

其实这个例子就是一个简单的单击图片实现图片放大的一个效果。

下面来分析一下这个是怎么实现的，首先打开编辑窗口你会看到如下：

[<img class="alignnone size-large wp-image-114" alt="QQ20140220-3" src="http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-3-1024x751.png" width="604" height="442" />][3]

&nbsp;

如果只是看这个界面还是不是很清楚怎么实现的图片缩放功能。。。  
仔细观察你会发现在Render in Image上写着Double click to open（Facebook的工程师还挺细心的呢，呵呵），既然这么写了，我们双击来打开看看有什么。

[<img class="alignnone size-large wp-image-115" alt="QQ20140220-4" src="http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-4-1024x751.png" width="604" height="442" />][4]

&nbsp;

首先关于Image跟Layer组件，当你拖拽一个图片到编辑窗口，默认会给你自动添加一个Layer，如果对于Layer不是很清楚的话，就可以理解成为UIImageView。

接下来是Interaction 2，看到这个奇怪的数字2肯定会觉得很好奇，为什么这个有个2呢？其实这个Origami是Facebook为Quartz Composer制作的一个插件，Quartz Composer这个工具已经有很长的历史了。关于Quartz Composer可以到苹果官方查看文档，地址如下：

https://developer.apple.com/library/mac/documentation/graphicsimaging/conceptual/quartzcomposeruserguide/qc\_intro/qc\_intro.html

也就是说本来就存在一个Interaction的Patch组件。只不过这个组件只是支持鼠标事件，对于移动平台的触摸事件不支持，所以Facebook从新制作了一个组件，并且命名为Interaction 2。

关于Switch如果直接翻译的话是开关，但是我觉得他其实就是一个状态机，就是表示某种状态的。比如当上面的Interaction接受的Layer的单机事件的话，他会告诉Switch有单击事件，并且将Switch的On/Off值设置为0跟1，接下来就会Bouncy Animation。  
并且Bouncy Animation的Progress的与俩个Transition链接。Transition就是来实现缩放的功能的。打开Patch Inspector会看到他有一个Start与End的值。

[<img class="alignnone size-medium wp-image-119" alt="QQ20140220-6" src="http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-6-201x300.png" width="201" height="300" />][5]

&nbsp;

通过Transition分别与Layer的Alpha与Scale相连接就能实现，单击屏幕实现图片的放大效果了。

实例的下载地址如下：

http://facebook.github.io/origami/examples/download/Alpha%20and%20Scale.qtz

&nbsp;

 [1]: http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-1.png
 [2]: http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-2.png
 [3]: http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-3.png
 [4]: http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-4.png
 [5]: http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140220-6.png