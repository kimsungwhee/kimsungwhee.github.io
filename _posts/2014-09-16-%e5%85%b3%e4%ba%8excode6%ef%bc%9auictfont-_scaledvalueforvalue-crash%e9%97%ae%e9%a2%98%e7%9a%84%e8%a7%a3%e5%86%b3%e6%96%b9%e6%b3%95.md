---
title: '关于Xcode6：[UICTFont _scaledValueForValue:] Crash问题的解决方法'
author: admin
layout: post
permalink: /%e5%85%b3%e4%ba%8excode6%ef%bc%9auictfont-_scaledvalueforvalue-crash%e9%97%ae%e9%a2%98%e7%9a%84%e8%a7%a3%e5%86%b3%e6%96%b9%e6%b3%95/
categories:
  - iOS
tags:
  - iOS8
  - UICTFont
  - xcode6
---
最近用Xcode6编译了一下以前的一个项目，突然发现在xcode6编译运行，马上机会Crash。

> <p class="p1">
>   <b>2014-09-16 14:14:15.303  *** Assertion failure in -[UICTFont _scaledValueForValue:], /SourceCache/UIFoundation_Sim/UIFoundation-371/UIFoundation/iOS/UIFont.m:</b>
> </p>

如果你的项目中存在类似的代码：

<pre class="lang:default decode:true ">[UIFont fontWithName:@"HiraKakuProN-W3" size:10]</pre>

&nbsp;

估计在xcode6的环境下编译会马上就会Crash掉，我的解决方法如下：

<pre class="lang:default decode:true ">[UIFont fontWithDescriptor:[UIFontDescriptor fontDescriptorWithFontAttributes:@{@"NSCTFontUIUsageAttribute" : UIFontTextStyleBody,@"NSFontNameAttribute" : @"HiraKakuProN-W3"}] size:10.0]</pre>

&nbsp;

替换成如上的方法就会顺利的编译通过。