---
title: Unity4.x实现Native App启动Unity,制作混合应用(Integrate Native iOS App into Unity App)
author: admin
layout: post
permalink: /unity4-x%e5%ae%9e%e7%8e%b0native-app%e5%90%af%e5%8a%a8unity%e5%88%b6%e4%bd%9c%e6%b7%b7%e5%90%88%e5%ba%94%e7%94%a8integrate-native-ios-app-into-unity-app/
categories:
  - iOS
  - Unity
tags:
  - hybrid
  - Unity4.3
  - 混合应用
---
相信很多朋友会遇到同样的问题，如何才能在iOS Native App里启动一个Unity App。  
比如制作一个应用程序需要通过Unity来演示三维画面，再或者是制作混合型的游戏的时候（内嵌UIWebView）。这个时候相信对于一直从事iOS开发的朋友一定非常想直接在iOS Native App里启动Unity。

如果是在Unity3.x的时候可以完全自由的通过自定义AppDelegate来实现自己需要的需求。然而从4.x之后Unity必须得让Unity首先被初始化。你可以让Unity进入暂停的状态。之后在需要的时候将Unity的View显示出来并且运行。

下面简单说明一下我的实现逻辑：

首先观察一下UnityAppController.mm文件

<pre class="lang:default decode:true">//当应用程序进入后台的时候，主要通过一下代码暂停Unity
- (void)applicationDidBecomeActive:(UIApplication*)application
{
	printf_console("-&gt; applicationDidBecomeActive()\n");
	if (_didResignActive)
		UnityPause(false);

	_didResignActive = NO;
}
//当恢复的时候通过一下代码
- (void)applicationWillResignActive:(UIApplication*)application
{
	printf_console("-&gt; applicationWillResignActive()\n");
	UnityPause(true);

	extern void UnityStopVideoIfPlaying();
	UnityStopVideoIfPlaying();

	_didResignActive = YES;
}</pre>

<!--more-->

看到如上的代码相信你也就知道大概怎么实现了，下面来具体介绍一下我的实现方法：

  * 首先，创建一个AppDelegate的类并且继承与UnityAppController

<pre class="lang:default decode:true" title="AppDelegate.h">#import "UnityAppController.h"
#import "MyViewController.h"

@interface AppDelegate : UnityAppController

@property (nonatomic, strong) UIWindow *window;
@property (nonatomic, strong) UnityAppController *unityVC;
@property (nonatomic, strong) MyViewController *myVC;

@end</pre>

&nbsp;

<pre class="lang:default decode:true" title="AppDelegate.m">#import "AppDelegate.h"

@implementation AppDelegate

@synthesize unityVC = _unityVC;
@synthesize window = _window;

- (UnityView*)initUnityViewImpl
{
    UIScreen *screen = [UIScreen mainScreen];
    CGRect screenRect = screen.bounds; 

    if (screenRect.size.width &lt; screenRect.size.height) {

        CGRect temp;
        temp.size.width = screenRect.size.height;
        temp.size.height = screenRect.size.width;
        screenRect = temp;
    }
    return [[UnityView alloc] initWithFrame:screenRect];
}

- (void)createViewHierarchyImpl{

    if (_unityVC == nil) { 
        _unityVC = [[UnityAppController alloc] init];
    }

    MyViewController *myVC = [[MyViewController alloc] initWithNibName:@"MyViewController" bundle:[NSBundle mainBundle]];

    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.rootViewController = myVC;
    self.window.clipsToBounds = NO;

    _rootController = self.window.rootViewController;
    _rootView = _rootController.view;

    [self.window makeKeyAndVisible];
    [self pauseUnity];
}

- (void)playUnity{
    [_unityVC unityPause:NO];
}

- (void)pauseUnity{
    [_unityVC unityPause:YES];
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{

    [super application:application didFinishLaunchingWithOptions:launchOptions];

    return YES;
}

@end

IMPL_APP_CONTROLLER_SUBCLASS(AppDelegate);</pre>

  * 接下来就该Main.mm文件

<pre class="lang:default decode:true" title="Main.mm">const char* AppControllerClassName = "AppDelegate";//修改为自己的实现的AppDelegate类</pre>

  *  UnityViewController里添加一个unityPause方法，主要是控制Unity的暂停

<pre class="lang:default decode:true">- (void)unityPause:(BOOL)pause{
    UnityPause(pause);
}</pre>

参考：

http://forum.unity3d.com/threads/191971-Unity-AppController-subclassing

http://answers.unity3d.com/questions/534197/unity-ios-launch-native-window-instead-of-unity.html