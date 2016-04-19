---
layout: post
title: 解决Openfire+Spark添加好友不成功的问题
date: 2014-01-17 10:10:21.000000000 +08:00
tags: 问题 & 原创
---


Openfire服务器搭建好后，使用spark通信没有问题，但是添加好友当时虽然可以成功，但是下次再登录时仍然看不到好友，原因是openfire 数据库导入mySQL时ofRoster的问题，table  “ofRoster”的属性jid 默认的是varchar(1024)， 而mySQL支持的varchar最大767，所以在创建表ofRoster失败，导致openfire无法添加好友，将mysql的sql文件 ofRoster表jid字段的varchar(1024) 改为varchar(760)就可以了。

打开/usr/local/openfire/resources/database下的openfire_mysql.sql文件，将第60行1024改为760。
如图

![](/assets/images/2014/20140117/1.png)
