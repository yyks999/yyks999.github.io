---
layout: post
title: MAC 10.9安装Ruby 2.1.0 与 Cocoapods
date: 2014-01-24 10:15:33.000000000 +08:00
tags: 转载 & 收藏
---

原文地址：http://blog.devtang.com/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/

感谢 [唐巧](http://blog.devtang.com/)


## 使用CocoaPods来做iOS程序的包依赖管理


2016.4.19日更新：Ruby下载地址已更新到2.3.0


## 前言
每种语言发展到一个阶段，就会出现相应的依赖管理工具, 或者是中央代码仓库。比如

Java: maven，Ivy
Ruby: gems
Python: pip, easy_install
Nodejs: npm
随着iOS开发者的增多，业界也出现了为iOS程序提供依赖管理的工具，这个工具叫：[CocoaPods](https://cocoapods.org/)


## CocoaPods简介

CocoaPods是一个负责管理iOS项目中第三方开源代码的工具。CocoaPods[项目的源码](https://github.com/CocoaPods/CocoaPods)在Github上管理。该项目开始于2011年8月12日，经过一年多的发展，现在已经超过1000次提交，并且持续保持活跃更新。开发iOS项目不可避免地要使用第三方开源库，CocoaPods的出现使得我们可以节省设置和更新第三方开源库的时间。

拿我之前开发的粉笔网iPhone客户端为例，其使用了14个第三方开源库。在没有使用CocoaPods以前，我需要：

把这些第三方开源库的相关文件复制到项目中，或者设置成git的submodule，然后这些开源库通常需要依赖系统的一些framework，我需要手工地将这些framework一一增加到项目依赖中，比如ASI网络库就需要增加以下framework: CFNetwork, SystemConfiguration, MobileCoreServices, CoreGraphics and zlib。
对于RegexKitLite这个正则表达式库，我还需要设置-licucore的编译参数
手工管理这些依赖包的更新。
这些体力活虽然简单，但毫无技术含量并且浪费时间。在使用CocoaPods之后，我只需要将用到的第三方开源库放到一个名为Podfile的文件中，然后执行pod install。CocoaPods就会自动将这些第三方开源库的源码下载下来，并且为我的工程设置好相应的系统依赖和编译参数。


## CocoaPods的安装和使用介绍


### 安装

安装方式异常简单, Mac下都自带ruby，使用ruby的gem命令即可下载安装：

```bash
$ sudo gem install cocoapods
$ pod setup
```

上面第二行执行时，会输出Setting up CocoaPods master repo，但是会等待比较久的时间。这步其实是Cocoapods在将它的信息下载到 ~/.cocoapods目录下，如果你等太久，可以试着cd到那个目录，用du -sh *来查看下载进度。

如果你的gem太老，可能也会有问题，可以尝试用如下命令升级gem:

```bash
sudo gem update --system
```

另外，ruby的软件源rubygems.org因为使用的亚马逊的云服务，所以被墙了，需要更新一下ruby的源：

```bash
gem sources --remove https://rubygems.org/
gem sources -a http://ruby.taobao.org/
gem sources -l
```


### 使用

使用时需要新建一个名为Podfile的文件，以如下格式，将依赖的库名字依次列在文件中即可

```bash
platform :ios
pod 'JSONKit',       '~> 1.4'
pod 'Reachability',  '~> 3.0.0'
pod 'ASIHTTPRequest'
pod 'RegexKitLite'
```

然后你将编辑好的Podfile文件放到你的项目根目录中，执行如下命令即可：

```bash
cd "your project home"
pod install
```

现在，你的所有第三方库都已经下载完成并且设置好了编译参数和依赖，你只需要记住如下2点即可：

	1.使用CocoaPods生成的 .xcworkspace 文件来打开工程，而不是以前的 .xcodeproj 文件。

	2.每次更改了Podfile文件，你需要重新执行一次pod install命令。


### 查找第三方库

你如果不知道cocoaPods管理的库中，是否有你想要的库，那么你可以通过pod search命令进行查找，以下是我用pod search json查找到的所有可用的库：

```bash
$ pod search json

-> AnyJSON (0.0.1)
   Encode / Decode JSON by any means possible.
   - Homepage: https://github.com/mattt/AnyJSON
   - Source:   https://github.com/mattt/AnyJSON.git
   - Versions: 0.0.1 [master repo]

-> JSONKit (1.5pre)
   A Very High Performance Objective-C JSON Library.
   - Homepage: https://github.com/johnezang/JSONKit
   - Source:   git://github.com/johnezang/JSONKit.git
   - Versions: 1.5pre, 1.4 [master repo]

-> MTJSONDictionary (0.0.4)
   An NSDictionary category for when you're working with it converting to/from JSON. DEPRECATED, use MTJSONUtils
   instead.
   - Homepage: https://github.com/mysterioustrousers/MTJSONDictionary.git
   - Source:   https://github.com/mysterioustrousers/MTJSONDictionary.git
   - Versions: 0.0.4, 0.0.3, 0.0.2 [master repo]

-> MTJSONUtils (0.1.0)
   An NSObject category for working with JSON.
   - Homepage: https://github.com/mysterioustrousers/MTJSONUtils.git
   - Source:   https://github.com/mysterioustrousers/MTJSONUtils.git
   - Versions: 0.1.0, 0.0.1 [master repo]

-> SBJson (3.1.1)
   This library implements strict JSON parsing and generation in Objective-C.
   - Homepage: http://stig.github.com/json-framework/
   - Source:   https://github.com/stig/json-framework.git
   - Versions: 3.1.1, 3.1, 3.0.4, 2.2.3 [master repo]

-> TouchJSON (1.0)
   TouchJSON is an Objective-C based parser and generator for JSON encoded data.
   - Homepage: https://github.com/TouchCode/TouchJSON
   - Source:   https://github.com/TouchCode/TouchJSON.git
   - Versions: 1.0 [master repo]
```


### 生成第三方库的帮助文档

如果你想让CococaPods帮你生成第三方库的帮助文档，并集成到XCode中，那么用brew安装appledoc即可：

```bash
brew install appledoc
```

关于appledoc，我在今年初的另一篇博客[《使用Objective-C的文档生成工具:appledoc》](http://blog.devtang.com/2012/02/01/use-appledoc-to-generate-xcode-doc/)中有专门介绍。它最大的优点是可以将帮助文档集成到XCode中，这样你在敲代码的时候，按住opt键单击类名或方法名，就可以显示出相应的帮助文档。


### 原理

大概研究了一下CocoaPods的原理，它是将所有的依赖库都放到另一个名为Pods项目中，然后让主项目依赖Pods项目，这样，源码管理工作都从主项目移到了Pods项目中。发现的一些技术细节有：

	1.Pods项目最终会编译成一个名为libPods.a的文件，主项目只需要依赖这个.a文件即可。

	2.对于资源文件，CocoaPods提供了一个名为Pods-resources.sh的bash脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中。

	3.CocoaPods通过一个名为Pods.xcconfig的文件来在编译时设置所有的依赖和参数。

Have fun!



Ruby 2.3.0下载地址：[ruby-2.3.0.zip](http://ftp.ruby-lang.org/pub/ruby/2.3/ruby-2.3.0.zip)
RubyGems 镜像地址： [Ruby China](https://gems.ruby-china.org/)

· Ruby 的详细介绍：[请点这里](http://www.oschina.net/p/ruby)
· Ruby 的下载地址：[请点这里](https://rubygems.org/)
· Ruby 的源码地址：[请点这里](https://github.com/rubygems/rubygems.org)

