---
title: '[Unity]内存管理及优化（转载）'
author: admin
layout: post
permalink: /unity%e5%86%85%e5%ad%98%e7%ae%a1%e7%90%86%e5%8f%8a%e4%bc%98%e5%8c%96%ef%bc%88%e8%bd%ac%e8%bd%bd%ef%bc%89/
categories:
  - Unity
tags:
  - 内存优化
  - 内存管理
---
<span>Unity3D在记忆体占用上一直被人诟病，特别是对于面向移动设备的游戏开发，动辄记忆体占用飙上一两百兆，导致记忆体资源耗尽，从而被系统强退造成极差的体验。类似这种情况并不少见，但是绝大部分都是可以避免的。虽然理论上Unity的记忆体管理系统应当为开发者分忧解难，让大家投身到更有意义的事情中去，但是对于Unity对记忆体的管理方式，官方文档中并没有太多的说明，基本需要依靠自己摸索。最近在接手的专案中存在严重的记忆体问题，在参照文档和Unity Answer众多猜测和证实之后，稍微总结了下Unity中的记忆体的分配和管理的基本方式，在此共用。</span>

<div>
  虽然Unity 标榜自己的记忆体使用全都是“Managed Memory” ，但是事实上你必须正确地使用记忆体，以保证回收机制正确运行。如果没有做应当做的事情，那么场景和代码很有可能造成很多非必要记忆体的占用，这也是很多Unity 开发者抱怨记忆体占用太大的原因。接下来我会介绍Unity 使用记忆体的种类，以及相应每个种类的优化和使用的技巧。遵循使用原则，可以让非必要资源尽快得到释放，从而降低记忆体占用。
</div>

&nbsp;

<hr align="center" size="2" width="100%" />

&nbsp;

<div>
  <strong>Unity 中的记忆体种类</strong>
</div>

<div>
  实际上Unity 游戏使用的记忆体一共有三种：程式码、托管堆（Managed Heap ）以及本机堆（Native Heap）。
</div>

&nbsp;

<div>
  程式码包括了所有的Unity 引擎，使用的库，以及你所写的所有的游戏代码。在编译后，得到的运行档将会被载入到设备中执行，并占用一定记忆体。这部分记忆体实际上是没有办法去“ 管理” 的，它们将在记忆体中从一开始到最后一直存在。一个空的Unity 默认场景，什么代码都不放，在iOS 设备上占用记忆体应该在17MB 左右，而加上一些自己的代码很容易就飙到20MB 左右。想要减少这部分记忆体的使用，能做的就是减少使用的库，稍后再说。
</div>

&nbsp;

<div>
  托管堆是被Mono 使用的一部分记忆体。<a href="http://www.mono-project.com/Main_Page">Mono</a>项目一个开源的.net 框架的一种实现，对于Unity 开发，其实充当了基本类库的角色。托管堆用来存放类的实例（比如用new 生成的清单，实例中的各种声明的变数等）。“托管” 的意思是Mono“ 应该” 自动地改变堆的大小来适应你所需要的记忆体，并且定时地使用垃圾回收（Garbage Collect ）来释放已经不需要的记忆体。关键在于，有时候你会忘记清除对已经不需要再使用的记忆体的引用，从而导致Mono 认为这块记忆体一直有用，而无法 ​​回收。
</div>

&nbsp;

<div>
  最后，本机堆是Unity 引擎进行申请和操作的地方，比如贴图，音效，关卡资料等。Unity 使用了自己的一套记忆体管理机制来使这块记忆体具有和托管堆类似的功能。基本理念是，如果在这个关卡里需要某个资源，那么在需要时就载入，之后在没有任何引用时进行卸载。听起来很美好也和托管堆一样，但是由于Unity 有一套自动载入和卸载资源的机制，让两者变得差别很大。自动载入资源可以为开发者省不少事儿，但是同时也意味着开发者失去了手动管理所有载入资源的权力，这非常容易导致大量的记忆体占用（贴图什么的你懂的），也是Unity 给人留下“ 吃记忆体” 印象的罪魁祸首。
</div>

&nbsp;

<hr align="center" size="2" width="100%" />

**优化程式码的记忆体占用**

<div>
  这部分的优化相对简单，因为能做的事情并不多：主要就是减少打包时的引用库，改一改build 设置即可。对于一个新专案来说不会有太大问题，但是如果是已经存在的专案，可能改变会导致原来所需要的库的缺失（虽说一般来说这种可能性不大），因此有可能无法做到最 ​​优。
</div>

&nbsp;

<div>
</div>

&nbsp;

<div>
  当使用Unity 开发时，默认的Mono 包含库可以说大部分用不上，在Player Setting （Edit->Project Setting->Player 或者Shift+Ctrl(Command)+B 里的Player Setting 按钮）面板里，将最下方的Optimization 栏目中“Api Compatibility Level” 选为.NET 2.0 Subset ，表示你只会使用到部分的.NET 2.0 Subset ，不需要Unity 将全部.NET 的Api 包含进去。接下来的“Stripping Level” 表示从build 的库中剥离的力度，每一个剥离选项都将从打包好的库中去掉一部分内容。你需要保证你的代码没有用到这部分被剥离的功能，选为“Use micro mscorlib” 的话将使用最小的库（一般来说也没啥问题，不行的话可以试试之前的两个）。库剥离可以极大地降低打包后的程式的尺寸以及程式码的记忆体占用，唯一的缺点是这个功能只支援Pro 版的Unity 。
</div>

&nbsp;

<div>
  这部分优化的力度需要根据代码所用到的.NET 的功能来进行调整，有可能不能使用Subset 或者最大的剥离力度。如果超出了限度，很可能会在需要该功能时因为找不到相应的库而crash 掉（iOS 的话很可能在Xcode 编译时就报错了）。比较好地解决方案是仍然用最强的剥离，并辅以较小的协力厂商的类库来完成所需功能。一个最常见问题是最大剥离时Sysytem.Xml 是不被Subset 和micro 支持的，如果只是为了xml ，完全可以导入一个羽量级的xml 库来解决依赖（Unity 官方推荐<a href="http://unity3d.com/support/documentation/Images/manual/Mono.Xml.zip">这个</a>）。
</div>

&nbsp;

<div>
  关于每个设定对应支持的库的详细列表，可以在<a href="http://docs.unity3d.com/Documentation/ScriptReference/MonoCompatibility.html">这里</a>找到。关于每个剥离级别到底做了什么，<a href="http://unity3d.com/support/documentation/Manual/iphone-playerSizeOptimization.html">Unity 的文档</a>也有说明。实际上，在游戏开发中绝大多数被剥离的功能使用不上的，因此不管如何，库剥离的优化方法都值得一试。
</div>

<!--more-->

<hr align="center" size="2" width="100%" />

**托管堆优化**

<div>
  Unity 有一篇不错的关于<a href="http://unity3d.com/support/documentation/Manual/Understanding%20Automatic%20Memory%20Management.html">托管堆代码如何写比较好</a>的说明，在此基础上我个人有一些补充。
</div>

&nbsp;

<div>
  首先需要明确，托管堆中存储的是你在你的代码中申请的记忆体（不论是用js ，C# 还是Boo 写的）。一般来说，无非是new 或者Instantiate 两种生成object 的方法（事实上Instantiate 中也是调用了new ）。在接收到alloc 请求后，托管堆在其上为要新 ​​生成的物件实例以及其执行个体变数分配记忆体，如果可用空间不足，则向系统申请更多空间。
</div>

&nbsp;

<div>
  当你使用完一个实例物件之后，通常来说在脚本中就不会再有对该物件的引用了（这包括将变数设置为null 或其他引用，超出了变数的作用域，或者对Unity 物件发送Destory() ）。在每隔一段时间，Mono 的垃圾回收机制将检测记忆体，将没有再被引用的记忆体释放回收。总的来说，你要做的就是在尽可能早的时间将不需要的引用去除掉，这样回收机制才能正确地把不需要的记忆体清理出来。但是需要注意在记忆体清理时有可能造成游戏的短时间卡顿，这将会很影响游戏体验，因此如果有大量的记忆体回收工作要进行的话，需要尽量选择合适的时间。
</div>

&nbsp;

<div>
  如果在你的游戏里，有特别多的类似实例，并需要对它们经常发送Destroy() 的话，游戏性能上会相当难看。比如<a href="http://www.onevcat.com/showcase/coinpush/">小熊推金币</a>中的金币实例，按理说每枚金币落下台子后都需要对其Destory() ，然后新的金币进入台子时又需要Instantiate ，这对性能是极大的浪费。一种通常的做法是在不需要时，不摧毁这个GameObject ，而只是隐藏它，并将其放入一个重用阵列中。之后需要时，再从重用阵列中找到可用的实例并显示。这将极大地改善游戏的性能，相应的代价是消耗部分记忆体，一般来说这是可以接受的。关于物件重用，可以参考<a href="http://docs.unity3d.com/Documentation/Manual/UnderstandingAutomaticMemoryManagement.html">Unity 关于记忆体方面的文档中 Reusable Object Pools 部分</a>，或者Prime31 有一个是用Linq 来建立重用池的视频教程（Youtube ，需要翻墙，<a href="http://www.youtube.com/watch?v=IX041ZvgQKE">上</a>，<a href="http://www.youtube.com/watch?v=d9078u8ft58">下</a>）。
</div>

&nbsp;

<div>
  如果不是必要，应该在游戏进行的过程中尽量减少对GameObject 的Instantiate() 和Destroy() 调用，因为对计算资源会有很大消耗。在可携式装置上短时间大量生成和摧毁物体的话，很容易造成暂态卡顿。如果记忆体没有问题的话，尽量选择先将他们收集起来，然后在合适的时候（比如按暂停键或者是关卡切换），将它们批量地销毁并且回收记忆体。Mono 的记忆体回收会在后台自动进行，系统会选择合适的时间进行垃圾回收。在合适的时候，也可以手动地调用System.GC.Collect() 来建议系统进行一次垃圾回收。要注意的是这里的调用真的仅仅只是建议，可能系统会在一段时间后在进行回收，也可能完全不理会这条请求，不过在大部分时间里，这个调用还是靠谱的。
</div>

&nbsp;

<hr align="center" size="2" width="100%" />

&nbsp;

<div>
  <strong>本机堆的优化</strong>
</div>

<div>
  当你载入完成一个Unity 的scene 的时候，scene 中的所有用到的asset （包括Hierarchy 中所有GameObject 上以及脚本中赋值了的的材质，贴图，动画，声音等素材），都会被自动载入（这正是Unity 的智慧之处）。也就是说，当关卡呈现在用户面前的时候，所有Unity 编辑器能认识的本关卡的资源都已经被预先加入记忆体了，这样在本关卡中，用户将有良好的体验，不论是更换贴图，声音，还是播放动画时，都不会有额外的载入，这样的代价是记忆体占用将变多。Unity 最初的设计目的还是面向桌上型电脑，几乎无限的记忆体和虚拟记忆体使得这样的占用似乎不是问题，但是这样的记忆体策略在之后移动平台的兴起和大量移动设备游戏的制作中出现了弊端，因为移动设备能使用的资源始终非常有限。因此在面向移动设备游戏的制作时，尽量减少在Hierarchy 对资源的直接引用，而是使用Resource.Load 的方法，在需要的时候从硬碟中读取资源，在使用后用Resource.UnloadAsset() 和Resources.UnloadUnusedAssets() 尽快将其卸载掉。总之，这里是一个处理时间和占用记忆体空间的trade off ，如何达到最好的效果没有标准答案，需要自己权衡。
</div>

&nbsp;

<div>
  在关卡结束的时候，这个关卡中所使用的所有资源将会被卸载掉（除非被标记了DontDestroyOnLoad ）的资源。注意不仅是DontDestroyOnLoad 的资源本身，其相关的所有资源在关卡切换时都不会被卸载。DontDestroyOnLoad 一般被用来在关卡之间保存一些玩家的状态，比如分数，级别等偏向文本的资讯。如果DontDestroyOnLoad 了一个包含很多资源（比如大量贴图或者声音等大记忆体占用的东西）的话，这部分资源在场景切换时无法卸载，将一直占用记忆体，这种情况应该尽量避免。
</div>

&nbsp;

<div>
  另外一种需要注意的情况是脚本中对资源的引用。大部分脚本将在场景转换时随之失效并被回收，但是，在场景之间被保持的脚本不在此列（通常情况是被附着在DontDestroyOnLoad 的GameObject 上了）。而这些脚本很可能含有对其他物体的Component 或者资源的引用，这样相关的资源就都得不到释放，这绝对是不想要的情况。另外，static 的单例（singleton ）在场景切换时也不会被摧毁，同样地，如果这种单例含有大量的对资源的引用，也会成为大问题。因此，尽量减少代码的耦合和对其他脚本的依赖是十分有必要的。如果确实无法避免这种情况，那应当手动地对这些不再使用的引用物件调用Destroy() 或者将其设置为null 。这样在垃圾回收的时候，这些记忆体将被认为已经无用而被回收。
</div>

<div>
  需要注意的是，Unity 在一个场景开始时，根据场景构成和引用关系所自动读取的资源，只有在读取一个新的场景或者reset 当前场景时，才会得到清理。因此这部分记忆体占用是不可避免的。在小记忆体环境中，这部分初始记忆体的占用十分重要，因为它决定了你的关卡是否能够被正常载入。因此在计算资源充足或是关卡开始之后还有机会进行载入时，尽量减少Hierarchy 中的引用，变为手动用Resource.Load ，将大大减少记忆体占用。在Resource.UnloadAsset() 和Resources.UnloadUnusedAssets() 时，只有那些真正没有任何引用指向的资源会被回收，因此请确保在资源不再使用时，将所有对该资源的引用设置为null 或者Destroy 。同样需要注意，这两个Unload 方法仅仅对Resource.Load 拿到的资源有效，而不能回收任何场景开始时自动载入的资源。与此类似的还有AssetBundle 的Load 和Unload 方法，灵活使用这些手动自愿载入和卸载的方法，是优化Unity 记忆体占用的不二法则～
</div>

<div>
</div>

<div>
  ps.看了这篇文章收益匪浅啊。。
</div>

<div>
</div>

<div>
  原文地址：
</div>

<div>
  http://sky-unity3d.blogspot.jp/2013/01/unity-3d.html
</div>