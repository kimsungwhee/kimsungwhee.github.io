---
title: '[Unity]关于iOS申请因为Advertising Identifier问题被拒绝的解决方法'
author: admin
layout: post
permalink: /unity%e5%85%b3%e4%ba%8eios%e7%94%b3%e8%af%b7%e5%9b%a0%e4%b8%baadvertising-identifier%e9%97%ae%e9%a2%98%e8%a2%ab%e6%8b%92%e7%bb%9d%e7%9a%84%e8%a7%a3%e5%86%b3%e6%96%b9%e6%b3%95/
categories:
  - Unity
tags:
  - ASIdentifierManager
  - IDFA
---
最近苹果对于没有使用广告的应用却调用了advertisingIdentifier的话会被决绝申请。

被Reject的内容如下：

> We found your app uses the iOS Advertising Identifier but does not include ad functionality. This does not comply with the terms of the iOS Developer Program License Agreement, as required by the App Store Review Guidelines.

主要的原因是Unity生成的Xcode项目里有一个文件调用了advertisingIdentifier，如果你的项目没有使用广告服务的话，请手动禁止调用。

步骤如下：

  * 首先在Xcode项目中找到DeviceSettings.mm文件

<img class="alignnone size-full wp-image-90" alt="QQ20140213-1@2x" src="http://www.iappfan.com/wp-content/uploads/2014/02/QQ20140213-1@2x.png" width="522" height="274" />

  * 打开文件并且删除下面的函数

<pre class="lang:default decode:true" title="将这些函数删除">static id QueryASIdentifierManager()

{

&lt;..&gt;

}

static void QueryAdID()

{

&lt;..&gt;

}

static void QueryAdTracking()

{

&lt;..&gt;

}</pre>

  * 将下面的静态变量删除

<pre class="lang:default decode:true">static NSString*    _ADID               = nil;

static bool         _AdTrackingEnabled  = false;</pre>

  *  找到对应的代码修改为如下

<pre class="lang:default decode:true">extern "C" const char*  UnityAdvertisingIdentifier()

{

return NULL;

}

extern "C" bool         UnityAdvertisingTrackingEnabled()

{

return false;

}

static void QueryDeviceID()

{

    if(_DeviceID == nil)

    {

    #if UNITY_PRE_IOS7_TARGET

        if(!_ios70orNewer)

            _InitDeviceIDPreIOS7();

    #endif

        // first check vendor id

        if(_DeviceID == nil)

        {

            QueryVendorID();

            _DeviceID = _VendorID;

        }

    }

}</pre>

&nbsp;

顺便贴出一下我修改之后的DeviceSettings.mm文件如下：

<pre class="lang:default decode:true">#include &lt;sys/types.h&gt;
#include &lt;sys/sysctl.h&gt;

#if UNITY_PRE_IOS7_TARGET
	#include &lt;sys/socket.h&gt;
	#include &lt;net/if.h&gt;
	#include &lt;net/if_dl.h&gt;
	#include &lt;CommonCrypto/CommonDigest.h&gt;

	static void _InitDeviceIDPreIOS7();
#endif

#include "DisplayManager.h"

static NSString*	_DeviceID			= nil;
static NSString*	_VendorID			= nil;

static NSString*	_DeviceName			= nil;
static NSString*	_SystemName			= nil;
static NSString*	_SystemVersion		= nil;

static NSString*	_DeviceModel		= nil;
static int			_DeviceGeneration	= deviceUnknown;
static float		_DeviceDPI			= -1.0f;

static void QueryDeviceID();
static void QueryAdID();
static void QueryAdTracking();
static void QueryVendorID();

static void QueryDeviceName();
static void QuerySystemName();
static void QuerySystemVersion();

static void QueryDeviceModel();
static void QueryDeviceGeneration();
static void EstimateDeviceDPI();

//
// unity interface
//

extern "C" const char*	UnityDeviceUniqueIdentifier()
{
	QueryDeviceID();
	return [_DeviceID UTF8String];
}
extern "C" const char*	UnityVendorIdentifier()
{
	QueryVendorID();
	return [_VendorID UTF8String];
}
extern "C" const char*	UnityAdvertisingIdentifier()
{
	return NULL;
}
extern "C" bool 		UnityAdvertisingTrackingEnabled()
{
	return false;
}
extern "C" const char*	UnityDeviceName()
{
	QueryDeviceName();
	return [_DeviceName UTF8String];
}
extern "C" const char*	UnitySystemName()
{
	QuerySystemName();
	return [_SystemName UTF8String];
}
extern "C" const char*	UnitySystemVersion()
{
	QuerySystemVersion();
	return [_SystemVersion UTF8String];
}
extern "C" const char*	UnityDeviceModel()
{
	QueryDeviceModel();
	return [_DeviceModel UTF8String];
}
extern "C" int			UnityDeviceGeneration()
{
	QueryDeviceGeneration();
	return _DeviceGeneration;
}
extern "C" float		UnityDeviceDPI()
{
	EstimateDeviceDPI();
	return _DeviceDPI;
}

//------------------------------------------------------------------------------
//
//

static void QueryDeviceID()
{
	if(_DeviceID == nil)
	{
	#if UNITY_PRE_IOS7_TARGET
		if(!_ios70orNewer)
			_InitDeviceIDPreIOS7();
	#endif

		// first check vendor id
		if(_DeviceID == nil)
		{
			QueryVendorID();
			_DeviceID = _VendorID;
		}
	}
}

static id QueryASIdentifierManager()
{
	return nil;
}

static void QueryAdID()
{

}

static void QueryAdTracking()
{

}

static void QueryVendorID()
{
	if(_VendorID == nil && [UIDevice instancesRespondToSelector:@selector(identifierForVendor)])
		_VendorID = (NSString*)[[[[UIDevice currentDevice] performSelector:@selector(identifierForVendor)] UUIDString] retain];
}

static NSString* QueryDeviceStringProperty(SEL prop)
{
	return [UIDevice instancesRespondToSelector:prop] ? [[[UIDevice currentDevice] performSelector:prop] retain] : nil;
}

static void QueryDeviceName()
{
	if(_DeviceName == nil)
		_DeviceName = QueryDeviceStringProperty(@selector(name));
}
static void QuerySystemName()
{
	if(_SystemName == nil)
		_SystemName = QueryDeviceStringProperty(@selector(systemName));
}
static void QuerySystemVersion()
{
	if(_SystemVersion == nil)
		_SystemVersion = QueryDeviceStringProperty(@selector(systemVersion));
}

static void QueryDeviceModel()
{
	if(_DeviceModel == nil)
	{
		size_t size;
		::sysctlbyname("hw.machine", NULL, &size, NULL, 0);

		char* model = (char*)::malloc(size + 1);
		::sysctlbyname("hw.machine", model, &size, NULL, 0);
		model[size] = 0;

		_DeviceModel = [[NSString stringWithUTF8String:model] retain];
		::free(model);
	}
}

static void QueryDeviceGeneration()
{
	if(_DeviceGeneration == deviceUnknown)
	{
		const char* model = UnityDeviceModel();

		if (!strcmp(model, "iPhone2,1"))
			_DeviceGeneration = deviceiPhone3GS;
		else if (!strncmp(model, "iPhone3,",8))
			_DeviceGeneration = deviceiPhone4;
		else if (!strncmp(model, "iPhone4,",8))
			_DeviceGeneration = deviceiPhone4S;
		else if (!strncmp(model, "iPhone6,",8))
			_DeviceGeneration = deviceiPhone5S;
		else if (!strcmp(model, "iPod1,1"))
			_DeviceGeneration = deviceiPodTouch1Gen;
		else if (!strcmp(model, "iPod2,1"))
			_DeviceGeneration = deviceiPodTouch2Gen;
		else if (!strcmp(model, "iPod3,1"))
			_DeviceGeneration = deviceiPodTouch3Gen;
		else if (!strcmp(model, "iPod4,1"))
			_DeviceGeneration = deviceiPodTouch4Gen;
		else if (!strncmp(model, "iPod5,",6))
			_DeviceGeneration = deviceiPodTouch5Gen;
		else if (!strcmp(model, "iPad1,1"))
			_DeviceGeneration = deviceiPad1Gen;

		// check iphone5c, ipad2 and ipad3 separately - they are special cases as apple reused major ver for different hws
		if(_DeviceGeneration == deviceUnknown)
		{
			if (!strncmp(model, "iPhone5,",8))
			{
				int rev = atoi(model+8);
				if (rev &gt;= 3) _DeviceGeneration = deviceiPhone5C; // iPhone5,3
				else		  _DeviceGeneration = deviceiPhone5;
			}
			else if (!strncmp(model, "iPad2,", 6))
			{
				int rev = atoi(model+6);
				if(rev &gt;= 5)	_DeviceGeneration = deviceiPadMini1Gen; // iPad2,5
				else			_DeviceGeneration = deviceiPad2Gen;
			}
			else if (!strncmp(model, "iPad3,", 6))
			{
				int rev = atoi(model+6);
				if(rev &gt;= 4)	_DeviceGeneration = deviceiPad4Gen; // iPad3,4
				else			_DeviceGeneration = deviceiPad3Gen;
			}
		}

		// completely unknown hw - just determine form-factor
		if(_DeviceGeneration == deviceUnknown)
		{
			if (!strncmp(model, "iPhone",6))
				_DeviceGeneration = deviceiPhoneUnknown;
			else if (!strncmp(model, "iPad",4))
				_DeviceGeneration = deviceiPadUnknown;
			else if (!strncmp(model, "iPod",4))
				_DeviceGeneration = deviceiPodTouchUnknown;

			_DeviceGeneration = deviceUnknown;
		}
	}
}

static void EstimateDeviceDPI()
{
	if(_DeviceDPI &lt; 0.0f)
	{
		float baseDPI 		= 160.0f; // phone-like devices
		float scaleFactor 	= 1.0f;

		const char* model = UnityDeviceModel();
		if(::strncmp(model, "iPad", 4) == 0)
		{
			if(UnityDeviceGeneration() == deviceiPadMini1Gen)	baseDPI = 167.0f;
			else												baseDPI = 130.0f;
		}

		if( [UIScreen instancesRespondToSelector:@selector(scale)] )
			scaleFactor = (float)[[UIScreen mainScreen] scale];

		_DeviceDPI = baseDPI * scaleFactor;
	}
}

//
// some higher-level helpers
//

extern "C" void QueryTargetResolution(int* targetW, int* targetH)
{
	enum
	{
		kTargetResolutionNative = 0,
		kTargetResolutionAutoPerformance = 3,
		kTargetResolutionAutoQuality = 4,
		kTargetResolution320p = 5,
		kTargetResolution640p = 6,
		kTargetResolution768p = 7
	};

	int targetRes = UnityGetTargetResolution();

	float resMult = 1.0f;
	if(targetRes == kTargetResolutionAutoPerformance)
	{
		switch(UnityDeviceGeneration())
		{
			case deviceiPhone4:		resMult = 0.6f;		break;
			case deviceiPad1Gen:	resMult = 0.5f;		break;
			default:				resMult = 0.75f;	break;
		}
	}

	if(targetRes == kTargetResolutionAutoQuality)
	{
		switch(UnityDeviceGeneration())
		{
			case deviceiPhone4:		resMult = 0.8f;		break;
			case deviceiPad1Gen:	resMult = 0.75f;	break;
			default:				resMult = 1.0f;		break;
		}
	}

	switch(targetRes)
	{
		case kTargetResolution320p:	*targetW = 320;	*targetH = 480;		break;
		case kTargetResolution640p:	*targetW = 640;	*targetH = 960;		break;
		case kTargetResolution768p:	*targetW = 768;	*targetH = 1024;	break;

		default:
			*targetW = GetMainDisplay()-&gt;screenSize.width * resMult;
			*targetH = GetMainDisplay()-&gt;screenSize.height * resMult;
			break;
	}
}

//
// gritty stuff
//

#if UNITY_PRE_IOS7_TARGET
	static void _InitDeviceIDPreIOS7()
	{
		static const int MD5_DIGEST_LENGTH = 16;

		// macaddr: courtesy of FreeBSD hackers email list
		int mib[6] = { CTL_NET, AF_ROUTE, 0, AF_LINK, NET_RT_IFLIST, 0 };
		mib[5] = ::if_nametoindex("en0");

		size_t len = 0;
		::sysctl(mib, 6, NULL, &len, NULL, 0);

		char* buf = (char*)::malloc(len);
		::sysctl(mib, 6, buf, &len, NULL, 0);

		sockaddr_dl*   sdl = (sockaddr_dl*)((if_msghdr*)buf + 1);
		unsigned char* mac = (unsigned char*)LLADDR(sdl);

		char macaddr_str[18]={0};
		::sprintf(macaddr_str, "%02X:%02X:%02X:%02X:%02X:%02X", *mac, *(mac+1), *(mac+2), *(mac+3), *(mac+4), *(mac+5));
		::free(buf);

		unsigned char hash_buf[MD5_DIGEST_LENGTH];
		CC_MD5(macaddr_str, sizeof(macaddr_str)-1, hash_buf);

		char uid_str[MD5_DIGEST_LENGTH*2 + 1] = {0};
		for(int i = 0 ; i &lt; MD5_DIGEST_LENGTH ; ++i)
			::sprintf(uid_str + 2*i, "%02x", hash_buf[i]);

		_DeviceID = [[NSString stringWithUTF8String:uid_str] retain];
	}
#endif</pre>

&nbsp;

接下来就可以再次提交申请了。祝你顺利通过。

2014/02/24追记

使用如下命令查找一下项目中哪里使用了Advertising Identifier。

<pre>➜  test  find . | grep -v .svn  | grep "\.a" | grep -v "\.app" | xargs grep advertisingIdentifier
Binary file ./Libraries/xxxxxxxxx.a matches</pre>

还可以通过Strings命令来查看字节码文件。  
首先将生成的ipa文件的后缀名改成zip，之后解压缩会生成一个文件夹，在文件夹内部有一个linux可执行文件名称基本为你的应用程序的名称。在终端之下如下命令：

<pre>➜  dev.app  strings dev{your app name} | grep advertisingIdentifier 
get_advertisingIdentifier can only be called from the main thread.
UnityEngine.iPhone::get_advertisingIdentifier
advertisingIdentifier</pre>

&nbsp;