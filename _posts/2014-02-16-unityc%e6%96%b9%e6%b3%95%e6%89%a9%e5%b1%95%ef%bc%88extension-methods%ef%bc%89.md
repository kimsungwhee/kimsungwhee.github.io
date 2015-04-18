---
title: '[Unity]C#方法扩展（Extension Methods）'
author: admin
layout: post
permalink: /unityc%e6%96%b9%e6%b3%95%e6%89%a9%e5%b1%95%ef%bc%88extension-methods%ef%bc%89/
categories:
  - Unity
tags:
  - 方法扩展
---
如果在objective-c的话可以使用category来实现方法的扩展，C#当中也有一个非常便捷的方法来实现同样的功能，那就是Extension Methods。

相信很多朋友在编写代码的时候会碰到如下的问题：

<pre class="lang:default decode:true ">using UnityEngine;
using System.Collections;

public class Player : MonoBehaviour 
{
    void Update () 
    {
        //Set new x position to 5
        transform.position = new Vector3(5f, transform.position.y, transform.position.z);
    }
}</pre>

这个代码知识单纯的将transform的x的值改变，y,z都没有发生改变，有没有什么简单的方法来实现同样的功能呢？答案当然是有的，那就是使用方法扩展。

<pre class="lang:default decode:true ">using UnityEngine;
using System.Collections;

//必须声明为静态类 
public static class Extensions
{
    //必须声明为静态方法
    //方法的第一个参数为this
    public static void SetPositionX(this Transform t, float newX)
    {
        t.position = new Vector3(newX, t.position.y, t.position.z);
    }
}</pre>

接下来就可以使用刚刚声明的方法扩展来修改一下代码：

<pre class="lang:default decode:true ">using UnityEngine;
using System.Collections;

public class Player : MonoBehaviour 
{
    void Update () 
    {
        //Set new x position to 5
        transform.SetPositionX(5f);//效果是一样的
    }
}</pre>

是不是代码变得简洁了许多，下面把常用的一些方法扩展总结一下：

<pre class="lang:default decode:true ">using UnityEngine;
using System.Collections;

public static class Extensions
{
    public static void SetPositionX(this Transform t, float newX)
    {
        t.position = new Vector3(newX, t.position.y, t.position.z);
    }

    public static void SetPositionY(this Transform t, float newY)
    {
        t.position = new Vector3(t.position.x, newY, t.position.z);
    }

    public static void SetPositionZ(this Transform t, float newZ)
    {
        t.position = new Vector3(t.position.x, t.position.y, newZ);
    }

    public static float GetPositionX(this Transform t)
    {
        return t.position.x;
    }

    public static float GetPositionY(this Transform t)
    {
        return t.position.y;
    }

    public static float GetPositionZ(this Transform t)
    {
        return t.position.z;
    }

    public static bool HasRigidbody(this GameObject gobj)
    {
        return (gobj.rigidbody != null);
    }

    public static bool HasAnimation(this GameObject gobj)
    {
        return (gobj.animation != null);
    }

    public static void SetSpeed(this Animation anim, float newSpeed)
    {
        anim[anim.clip.name].speed = newSpeed; 
    }
}</pre>

&nbsp;