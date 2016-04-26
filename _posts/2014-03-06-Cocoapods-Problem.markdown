---
layout: post
title: Cocoapods 常见问题解决方案
date: 2014-03-06 17:51:06.000000000 +08:00
tags: 问题 & 原创
---


#### (1). Cocoapods error: “Pull is not possible because you have unmerged files.”

解决方法：

```bash
$ pod repo remove master
$ pod setup
```
 
或者 直接手动删除

```bash
rm -rf ~/.cocoapods/
```
 
然后执行 pod install 即可

 
#### (2).卡在Setting up CocoaPods master repo

解决方法：

可以在终端里使用命令：cd ~/.cocoapods进入这个文件

再使用：du -sh来查看下载进度

如果进度一直不变，说明源地址已被墙了，需要更换源地址，淘宝源速度比较快。

更换淘宝源 安装教程有提到： [iOS开发中使用CocoaPods来管理第三方的依赖程序](http://yyks999.github.io/2014/02/Cocoapods-Manage-ThirdLib/)

 
#### (3).更新gem失败
运行gem update –system时，出现类似

ERROR:  While executing gem … (Gem::FilePermissionError)

    You don’t have write permissions for the /Library/Ruby/Gems/2.0.0 directory.

的提示。

解决方法:权限问题，加上sudo即可。

```bash
sudo gem update –system
```
