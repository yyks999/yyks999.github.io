---
layout: post
title: Openfire 服务器更换为本地IP的方法
date: 2014-02-11 00:00:00.000000000 +08:00
tags: 转载 & 收藏
---

原文地址：http://blog.csdn.net/happysheepherder/article/details/4707124
感谢 [happysheepherder](http://www.cnblogs.com/wengzilin/)


如果你的服务器名称和mysql的地址都是使用的静态ip地址配置的，更改ip后，openfire就会开启失败，这种情况下请看下面的解决方法。

比如你的ip地址由 192.168.1.104 改为192.168.1.150后，openfire开启失败，控制台会出现一些红字，openfire database error …. ，解决方法：

#### Win系统：打开E:/Program Files/Openfire/con/openfire.xml, 修改serverURL>jdbc:mysql://192.168.0.111:3306/openfire 字段中的ip更改为新的ip，其实这里最好写成127.0.0.1 比较好，这样就始终指向本机。

#### Mac系统： 打开/usr/local/openfire/conf/openfire.xml，之后操作同上。


这样mysql数据库连接成功，openfire管理界面可以正常登录，但是客户端还是无法连接openfire，这是由于服务器名称还是以前的ip造成的，解决方法：

	1.登录openfire管理页面，在主页面下方选择编辑服务器属性，修改新的服务器名称为新的ip地址，也就是192.168.1.150，点击保存属性，页面提示从启服务器。

	2.重启后服务器名称出现一个叹号，鼠标放上去显示Found RSA certificate that is not valid for the server domain， 这样由于RSA认证无效造成的，需要对新的ip地址进行RSA证书的配置。

	3.选择【服务器配置】菜单，选择左下方的【服务器证书】，会看到两个证书，点击后面的删除按钮，全部删除，删除后系统提示重启服务器，点击重启

	4.重启后，系统提示“一个或更多的证书丢失。单击这里产生自定义签名证书”，点击这里，自动生成和新的ip匹配的RSA证书，生成后，系统提示重启。

	5.再次登录后，会看到主界面的服务器名称的叹号消失了，openfire正常，客户端可以正常登录了

 

PS.若遇到修改IP后程序一直报didNotAuthenticate验证失败的，只需重启下电脑即可。