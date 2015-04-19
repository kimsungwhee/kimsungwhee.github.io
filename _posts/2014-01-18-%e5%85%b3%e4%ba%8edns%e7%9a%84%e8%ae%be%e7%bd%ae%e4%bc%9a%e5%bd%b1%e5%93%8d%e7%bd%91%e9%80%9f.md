---
title: DNS的设置会影响网速吗？
author: admin
layout: post
permalink: /%e5%85%b3%e4%ba%8edns%e7%9a%84%e8%ae%be%e7%bd%ae%e4%bc%9a%e5%bd%b1%e5%93%8d%e7%bd%91%e9%80%9f/
categories:
  - 杂谈
tags:
  - 8.8.8.8
  - DNS
  - ISP
---
现在回想起来突然枉然大悟的感觉，大概在俩年前自己终于在海外生活中有了一个自己的小窝。可以自己一个人住了，以前一直都是跟朋友合租的。搬到新家之后，必不可少的得安装宽带。大概等待俩个星期之后家里的宽带终于安装上了。不过说实话，日本的宽带速度真的很给力。然而好事总是短暂的，大概半年之后突然发现访问国内的网站网速变得非常的慢，一直以为是ISP限制我的网速，要不然就是现在使用的这个ISP不怎么好，因为价格相对于其他的ISP来说便宜很多。一直犹豫着到底换不换ISP呢。就这样俩年多都过去了。

今天突然有个想法到底是什么影响网速的呢？

首先用traceroute测试了一下，发现有几个点非常的慢。而且通过IP查询，发现竟然跳转的路径很奇怪。  
[<img class="alignnone size-full wp-image-52" alt="QQ20140118-1@2x" src="http://kimsungwhee.com/wp-content/uploads/2014/01/QQ20140118-1@2x.png" width="1016" height="674" />][1]

发现210.130.1.1这个服务器很慢，到http://whatismyipaddress.com/搜索了一下，发现这个服务器所在地在东京，按理来说居然在东京的话，应该不会出现这么大的延迟现象啊。

接着继续查了一下，另一个Ip 202.97.60.34  
[<img class="alignnone size-large wp-image-53" alt="QQ20140118-2@2x" src="http://kimsungwhee.com/wp-content/uploads/2014/01/QQ20140118-2@2x-629x1024.png" width="604" height="983" />][2]

总算是到中国了，然而为什么要去这么内陆的地方呢？不应该是去上海或者是北京之类的地方吗。当然这个网站提供的数据不一定百分之白正确。  
调查到这里突然感觉，这个难道会不会是DNS的设置所影响的呢，以前一直使用的8.8.8.8这个google的DNS服务器。关于Google的DNS服务器可以到[wiki][3]看一下。原本以为使用Google的DNS服务器会加快网速，结果没有想到。。。。

改回默认ISP提供的DNS服务器之后，结果太让我意外了。走的路径完全不一样并且网站访问速度有了明显的提高。  
[<img class="alignnone size-full wp-image-54" alt="QQ20140118-3@2x" src="http://kimsungwhee.com/wp-content/uploads/2014/01/QQ20140118-3@2x.png" width="1014" height="838" />][4]

&nbsp;

结论：

如果你在访问一些比较特殊的网站或者被ISP禁用的网站的话，可以考虑使用Google的DNS，毕竟上帝为了开了一扇门也会为你关掉一扇门。

 [1]: http://kimsungwhee.com/wp-content/uploads/2014/01/QQ20140118-1@2x.png
 [2]: http://kimsungwhee.com/wp-content/uploads/2014/01/QQ20140118-2@2x.png
 [3]: http://zh.wikipedia.org/wiki/Google_Public_DNS
 [4]: http://kimsungwhee.com/wp-content/uploads/2014/01/QQ20140118-3@2x.png