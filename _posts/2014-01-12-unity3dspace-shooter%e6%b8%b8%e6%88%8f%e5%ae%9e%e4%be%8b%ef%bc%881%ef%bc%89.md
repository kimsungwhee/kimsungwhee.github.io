---
title: '[Unity3D]Space Shooter游戏实例（1）'
author: admin
layout: post
permalink: /unity3dspace-shooter%e6%b8%b8%e6%88%8f%e5%ae%9e%e4%be%8b%ef%bc%881%ef%bc%89/
categories:
  - Unity
tags:
  - Space Shooter
  - 游戏实例
---
<span style="line-height: 1.5;">最近Unity官方退出了一个Space Shooter游戏教程，今天就按照官方教程的步骤来创建太空射击游戏。</span>

官方教程地址如下：

http://unity3d.com/learn/tutorials/projects/space-shooter

  1. 创建项目

打开Unity创建一个新的项目，命名为Space Shooter保存。  
[<img class="alignnone size-full wp-image-35" alt="屏幕快照 2014-01-12 12.40.31" src="http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.40.31.png" width="710" height="449" />][1]

接下来创建游戏项目  
[<img class="alignnone size-full wp-image-36" alt="屏幕快照 2014-01-12 12.41.38" src="http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.41.38.png" width="449" height="427" />][2]

接下来从Asset Store下载Unity Projects:Space Shooter,并且导入到项目中。该资源包里包含了，本次游戏开发所需要的资源。以及一个已经完成的项目。（可以在Project/Assets/Done/Done_Scenes看到已经完成的一个Scene，可以自己先运行一下看看最终完成的游戏效果）  
[<img class="alignnone size-full wp-image-37" alt="屏幕快照 2014-01-12 12.43.05" src="http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.43.05.png" width="462" height="367" />][3]

Unity3D可以针对不同平台来开发游戏，首先在菜单File里选择Build Setting。设置Platfrom为Web Player。（可以根据自己的需要来设置发布的平台，本次按照官方教程将其设置为Web Player）  
[<img class="alignnone size-full wp-image-38" alt="屏幕快照 2014-01-12 12.49.38" src="http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.49.38.png" width="548" height="595" />][4]  
接下来设置一下，游戏的分辨率。主要是因为本次开发的游戏为竖屏游戏。  
[<img class="alignnone size-large wp-image-39" alt="QQ20140112-1" src="http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-1-869x1024.png" width="604" height="711" />][5]接下来把刚才导入的资源包中的飞船模型添加到场景中，拖动vehicle_playerShip到Hierarchy里并且从新命名为Player。  
[<img class="alignnone size-large wp-image-40" alt="QQ20140112-2" src="http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-2-1024x412.png" width="604" height="243" />][6][<img class="alignnone size-large wp-image-41" alt="QQ20140112-3" src="http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-3-1024x323.png" width="604" height="190" />][7]

接下来为Player游戏对象添加一个Rigidbody组件，并且取消使用重力选项。  
[<img class="alignnone size-full wp-image-42" alt="QQ20140112-4" src="http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-4.png" width="598" height="998" />][8]

下一步，给飞船添加一个碰撞检测的Collider,可以使用Capsule Collider。也可以使用Mesh Collider。因为该项目资源当中一种提供的Mesh Collider的模型，所以直接创建一个Mesh Collider，将Mesh Collider的Mesh设置为Models当中的vehicle\_playerShip\_collider。  
[<img class="alignnone size-full wp-image-43" alt="QQ20140112-5" src="http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-5.png" width="598" height="230" />][9]下一步为飞船添加引擎效果的Particle。直接把Prefabs/VFX/Engines/engines_player添加到Player里。将其设为Player的子物体。

[<img class="alignnone size-large wp-image-44" alt="QQ20140112-6" src="http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-6-1024x410.png" width="604" height="241" />  
][10]

到这里已经基本把我们的飞船基本设置完毕了，今天就写到这里吧。

 [1]: http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.40.31.png
 [2]: http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.41.38.png
 [3]: http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.43.05.png
 [4]: http://www.iappfan.com/wp-content/uploads/2014/01/屏幕快照-2014-01-12-12.49.38.png
 [5]: http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-1.png
 [6]: http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-2.png
 [7]: http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-3.png
 [8]: http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-4.png
 [9]: http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-5.png
 [10]: http://www.iappfan.com/wp-content/uploads/2014/01/QQ20140112-6.png