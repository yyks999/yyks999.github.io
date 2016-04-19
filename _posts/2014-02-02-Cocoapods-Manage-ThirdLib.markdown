---
layout: post
title: iOS开发中使用CocoaPods来管理第三方的依赖程序
date: 2014-02-02 10:15:33.000000000 +08:00
tags: 教程 & 原创
---

CocoaPods项目地址：[下载](https://github.com/CocoaPods/CocoaPods)

```bash
$ gem install cocoapods
$ pod setup
```

使用上述的命令进行安装，安装的过程可能持续1-3分钟，很容易让人以为挂掉了。。

创建一个名为Podfile的文件，我直接用Sublime创建的，然后放置在我的项目文件夹下面。与.xcodeproj同级的目录

还有就是更新，当Podfile需要更新的时候，需要重新处理，执行：

```bash
$ pod install
```


题外：

发现很多项目使用了CocoaPods，之前看一些开源项目的时候也没有留意，里面的Pods文件，后面自己实践了之后才知道。

比如[WordPress](https://github.com/wordpress-mobile/WordPress-iOS)的iOS客户端，就使用了CocoaPods


其它的参考链接，CocoaPods的原文链接：

http://docs.cocoapods.org/guides/getting_started.html

http://docs.cocoapods.org/podfile.html

http://docs.cocoapods.org/guides/installing_cocoapods.html

