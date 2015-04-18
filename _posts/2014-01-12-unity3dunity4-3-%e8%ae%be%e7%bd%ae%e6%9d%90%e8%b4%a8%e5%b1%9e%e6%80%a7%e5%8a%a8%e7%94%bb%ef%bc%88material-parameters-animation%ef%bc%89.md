---
title: '[Unity3D]Unity4.3 设置材质属性动画（Material parameters Animation）'
author: admin
layout: post
permalink: /unity3dunity4-3-%e8%ae%be%e7%bd%ae%e6%9d%90%e8%b4%a8%e5%b1%9e%e6%80%a7%e5%8a%a8%e7%94%bb%ef%bc%88material-parameters-animation%ef%bc%89/
categories:
  - Unity
tags:
  - Animation
  - Material
  - parameters
  - Unity4.3
---
今天发现4.3之后的Animaiton窗口跟以前不一样了。不能直接设置材质的offset动画了。后来在Unity的官方论坛发现了有同样困扰的人发的[帖子][1]。希望简单分享一下我实现的方法吧。直接上图

<img class="alignnone size-large wp-image-29" alt="material" src="http://www.iappfan.com/wp-content/uploads/2014/01/material-1024x349.png" width="604" height="205" />

实际上就是4.3之后的材质选项被归类到网格的下面，并且名称也发生了变化。

以前的Main Tex.offset.v 变更为 Mesh renderer.Material.\_Main Tex\_ST

&nbsp;

 [1]: http://forum.unity3d.com/threads/211535-Problem-with-animation-material-parameters-in-Unity4-3