---
title: Swift Singleton(单例)
author: admin
layout: post
permalink: /swift-singleton%e5%8d%95%e4%be%8b/
categories:
  - Swift
tags:
  - Singleton
  - 单例
---
Swift Singleton主要实现方法总结：

  * 全局常量

<pre class="lang:default decode:true ">let _SingletonASharedInstance = SingletonA()

class SingletonA  {

    class var sharedInstance : SingletonA {
        return _SingletonASharedInstance
    }

}</pre>

  *  嵌套结构

<pre class="lang:default decode:true">class SingletonB {

    class var sharedInstance : SingletonB {
        struct Static {
            static let instance : SingletonB = SingletonB()
        }
        return Static.instance
    }

}</pre>

  * Dispatch_onec

<pre class="lang:default decode:true ">class SingletonC {

    class var sharedInstance : SingletonC {
        struct Static {
            static var onceToken : dispatch_once_t = 0
            static var instance : SingletonC? = nil
        }
        dispatch_once(&Static.onceToken) {
            Static.instance = SingletonC()
        }
        return Static.instance!
    }
}</pre>

&nbsp;