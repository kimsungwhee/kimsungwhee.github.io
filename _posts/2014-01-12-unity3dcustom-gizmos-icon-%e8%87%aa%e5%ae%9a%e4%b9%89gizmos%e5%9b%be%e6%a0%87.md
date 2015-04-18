---
title: '[Unity3D]Custom Gizmos Icon 自定义Gizmos图标'
author: admin
layout: post
permalink: /unity3dcustom-gizmos-icon-%e8%87%aa%e5%ae%9a%e4%b9%89gizmos%e5%9b%be%e6%a0%87/
categories:
  - Unity
tags:
  - Custom
  - Gizmos
  - 自定义
---
有的时候当创建空的游戏对象的时候便于在场景中观察这个空的游戏对象可以为这个游戏对象创建自定义Gizmos图标。首先在Project创建一个Gizmos文件夹，将需要设置的图标图片放入其中。  
创建一个脚本文件CustomGizmos

<pre class="lang:c# decode:true " title="自定义Gizmos图标">void OnDrawGizmos()
{
  Gizmos.DrawIcon(transform.position,"icon.png",true);
}</pre>

&nbsp;