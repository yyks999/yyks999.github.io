---
layout: post
title: Cocoapods 常见问题解决方案
date: 2014-03-11 10:25:38.000000000 +08:00
tags: 问题 & 原创
---


使用了一段时间CocoaPods来管理Objective-c的类库，方便了不少。但是有一个小问题，当我在xcode输入import关键字的时候，没有自动联想补齐代码的功能，需要手工敲全了文件名，难以适应。


在stackoverflow上找到了[解决办法](http://stackoverflow.com/questions/12002905/ios-build-fails-with-cocoapods-cannot-find-header-files):

> Go to the Target > \”Build Settings\” tab and find the \”User Header Search Paths\” setting.
> Set this to \”$(BUILT_PRODUCTS_DIR)\” and check the \”Recursive\” check box.
> Now the built target will search the workspace’s shared build directory to locate the linkable header files.


简单说就是这么几步:

* 选择Target -> Build Settings 菜单，找到\”User Header Search Paths\”设置项
* 新增一个值$(BUILT_PRODUCTS_DIR)，并且选择\”Recursive\”，这样xcode就会在项目目录中递归搜索文件

自动补齐功能就OK了。
