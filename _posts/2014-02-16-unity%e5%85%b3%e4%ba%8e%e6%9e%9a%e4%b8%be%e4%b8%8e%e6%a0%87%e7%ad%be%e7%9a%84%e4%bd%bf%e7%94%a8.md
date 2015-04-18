---
title: '[Unity]关于枚举与标签的使用'
author: admin
layout: post
permalink: /unity%e5%85%b3%e4%ba%8e%e6%9e%9a%e4%b8%be%e4%b8%8e%e6%a0%87%e7%ad%be%e7%9a%84%e4%bd%bf%e7%94%a8/
categories:
  - Unity
tags:
  - 枚举
  - 标签
---
在游戏游戏开发的时候，经常会使用一种方式来表示当前的一种状态。

  * 使用常规的变量来表示一种游戏状态

<pre class="lang:default decode:true">using UnityEngine;

public class Player : MonoBehaviour
{
    public string status;//定义游戏玩家的状态

    void Update()
    {
        if (status == "Poison")
        {
            //当状态为Poison的时候的效果
        }
        else if (status == "Slow")
        {
            //当状态为slow的时候的效果
        }
        else if (status == "Mute")
        {
            //当状态为mute的时候的状态
        }
    }
}</pre>

如果按照上面的方法，你当然可以根据一种当前的status来判断较色的状态来实现对应的效果。

但是如果你在编写代码的时候出现了如下拼写错误的话

<pre class="lang:default decode:true">status = "posion";//拼写错误</pre>

编译器在编译代码的时候并不会给你提示代码错误，当游戏真正运行之后会发生与你预想的效果不一样。毕竟我们不是英语系国家，肯定难免避免不了拼写错误。为了防止以上的问题发生该怎么办呢？

  * 定义枚举

为了解决上面出现问题，我将修改代码为如下：

<pre class="lang:default decode:true">using UnityEngine;

public class Player : MonoBehaviour
{
    //在这里定义一组状态效果
    public enum StatusEffect
    {
        None,
        Poison,
        Slow,
        Mute
    }

    //将枚举定义为一个公开的成员变量
    public StatusEffect status;

    void Update()
    {
        if (status == StatusEffect.Poison)
        {
            //效果代码
        }
        else if (status == StatusEffect.Slow)
        {
            //效果代码
        }
        else if (status == StatusEffect.Mute)
        {
            //效果代码
        }
    }
}</pre>

按照以上使用枚举的方法，同样可以轻易的实现游戏状态的定义。接下来如果我们将代码拼写错误的话，编译器会自动提示错误。并且告知你该枚举类型内并不存在你所编写的状态。

<pre class="lang:default decode:true">status = StatusEffect.posion;//提示错误</pre>

使用枚举还有一个好处就是Unity会识别枚举类型，并且在Editor里一下拉列表的形式来显示出来。

<img class="alignnone size-full wp-image-98" alt="enum" src="http://www.iappfan.com/wp-content/uploads/2014/02/enum.png" width="800" height="395" />

  * 枚举作为位标签

<pre class="lang:default decode:true">[System.Flags]
public enum StatusEffect
{
    None   = 1 &lt;&lt; 0, // 1
    Poison = 1 &lt;&lt; 1, // 2
    Slow   = 1 &lt;&lt; 2, // 4
    Mute   = 1 &lt;&lt; 3  // 8
}</pre>

可以使用[System.Flags]来声明枚举为位标签，但是需要注意的是在声明时值需要以2的幂次方来声明。接下来我们就可以使位运算来识别当前的状态了。

什么时候需要使用这样的方法呢？如果想要判断一个状态为同时多种状态的时候就需要使用标签来实现，具体的方法如下：

<pre class="lang:default decode:true">//描述2种状态
status = StatusEffect.Poison | StatusEffect.Slow;

//当然也可以首先设置一种状态
status = StatusEffect.Poison;

//之后添加一种状态
status |= StatusEffect.Slow;

//删除一种状态
status &= ~StatusEffect.Poison;

//最后在update当中判断
if ((status & StatusEffect.Poison) == StatusEffect.Poison)
{
    //相应的效果
}</pre>

&nbsp;