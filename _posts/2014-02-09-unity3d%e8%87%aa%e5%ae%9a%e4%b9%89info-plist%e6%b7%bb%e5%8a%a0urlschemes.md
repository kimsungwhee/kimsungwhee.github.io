---
title: Unity3D自定义Info.plist添加URLSchemes
author: admin
layout: post
permalink: /unity3d%e8%87%aa%e5%ae%9a%e4%b9%89info-plist%e6%b7%bb%e5%8a%a0urlschemes/
categories:
  - Unity
tags:
  - Info.plist
  - Xcode
---
当使用Unity生成iOS项目的时候，经常会遇到一些需要自定义info.plist的情况。  
将下面的文件放到Assets/Editor/下面文件名命名为PostprocessBuildPlayer。

<pre class="lang:python decode:true " title="添加URL Schemes">#!/usr/bin/env python

import sys, os.path

install_path = sys.argv[1]
target_platform = sys.argv[2]

if target_platform != "iPhone": sys.exit()

info_plist_path = os.path.join(install_path, 'Info.plist')

file = open(info_plist_path, 'r')
plist = file.read()
file.close()

elements_to_add = '''
&lt;key&gt;CFBundleURLTypes&lt;/key&gt;
&lt;array&gt;
 &lt;dict&gt;
  &lt;key&gt;CFBundleURLSchemes&lt;/key&gt;
  &lt;array&gt;
   &lt;string&gt;自定义URL&lt;/string&gt;
  &lt;/array&gt;
 &lt;/dict&gt;
&lt;/array&gt;
'''

plist = plist.replace('&lt;key&gt;', elements_to_add + '&lt;key&gt;', 1)

file = open(info_plist_path, 'w')
file.write(plist)
file.close()</pre>

按照以上方法可以实现添加URLSchemes。