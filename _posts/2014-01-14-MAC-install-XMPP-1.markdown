---
layout: post
title: IOS即时通讯客户端开发之 － MAC上搭建Openfire服务器
date: 2014-01-14 17:10:33.000000000 +09:00
---

原文地址：http://www.cnblogs.com/xiaodao/archive/2013/04/05/3000554.html

感谢 月光的尽头


### 一、下载并安装openfire

#### 1.到http://www.igniterealtime.org/downloads/index.jsp下载最新openfire for mac版

比如：Openfire 3.8.1，下载后的文件：openfire_3_8_1.dmg

#### 2.点击安装，并执行默认操作

![](/assets/images/2014/20140114_1/1.png)

#### 3.启动openfire服务

在系统偏好设置的其他里，点击openfire偏好

![](/assets/images/2014/20140114_1/2.png)

启动后，点击Open Admin Console按钮，自动在浏览器中打开本地web配置页面http://localhost:9090/setup/index.jsp


### 二、配置openfire服务器

#### 1.设置语言，选中文

![](/assets/images/2014/20140114_1/3.png)


#### 2.主机设置

设置主机的访问ip地址

![](/assets/images/2014/20140114_1/4.png)

注意：域不能是机器名，否则会如下错误：

HTTP ERROR: 500 INTERNAL_SERVER_ERROR

本地的域，要设置为127.0.0.1


#### 3.数据库设置

如果要设置外部数据库（推荐，比如：MySQL），选择标准数据库连接

![](/assets/images/2014/20140114_1/5.png)


#### 4.设置数据库连接

![](/assets/images/2014/20140114_1/6.png)

（1）数据库驱动选择：MySQL，前提是已安装MySQL（具体的安装方法可以参考上一篇：mac上安装MySQL）

（2）JDBC驱动，默认不变

```bash
com.mysql.jdbc.Driver
```

（3）数据库URL：

形式如下：

```bash
jdbc:mysql://你的主机名:端口号/数据库名称
```

这里设置为

```bash
jdbc:mysql://localhost:3306/openfire
```

其中主机名[host-name]改为localhost，

其中数据库名称[database-name]改为openfire

解决数据库字符编码问题，可以在后面加

```bash
?useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8
```

最终的url形式是

```bash
jdbc:mysql://localhost:3306/openfire?useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8
```

注意：前提是已存在一个名为openfire的数据库，否则会报如下错误，连接配置不成功

The Openfire database schema does not appear to be installed. Follow the installation guide to fix this error. 

前期的MySQL数据库准备工作如下：

<1>设置/usr/local/openfire文件夹的访问权限为可读写

方法1：在finder中前往文件夹/usr/local/，右键openfire文件夹，显示简介

点击如图右下角中的锁图标解锁，并设置权限为：可以读写

![](/assets/images/2014/20140114_1/7.png)

方法2：打开终端，输入如下命令

```bash
sudo chmod 777 /usr/local/openfire
```

其中777表示授权可读写权限，000表示无访问权限

<2>在终端中，登陆MySQL

```bash
mysql -u root -p
```

然后输入数据库的root密码

<3>创建数据库openfire

```bash
create database openfire;
```

<4>导入openfire资源文件夹 resources/database下的数据表

```bash
use openfire;
source /usr/local/openfire/resources/database/openfire_mysql.sql
```

 在终端出现一排导入过程

![](/assets/images/2014/20140114_1/8.png)

<5>刷新权限

```bash
flush privileges;
```

<6>退出MySQL

```bash
exit
```

（4）用户名和密码

这里的用户名密码，是访问MySQL数据库时使用的帐号：root，和安装MySQL设置的root密码


#### 5.特性设置

如果不打算使用LDAP，则保持默认设置

![](/assets/images/2014/20140114_1/9.png)


#### 6.设置openfire服务器管理员的帐号和密码

![](/assets/images/2014/20140114_1/10.png)

可以随便填写一个管理员邮箱，输入要设置的密码

完成注册

![](/assets/images/2014/20140114_1/11.png)


#### 7.登陆管理控制台
 
默认的管理员帐号是“admin”，默认管理员密码“admin”，如果上面设置了新密码，则管理员密码是新密码

![](/assets/images/2014/20140114_1/12.png)

如果想去掉默认的admin帐号，并自定义，需要如下操作

 
（1）在终端中，登陆具体的数据库（openfire）

```bash
mysql -u root -p openfire
```

然后输入数据库的root密码

 
（2）删除表“ofUser”中的admin帐户

```bash
delete from ofUser where username='admin';
```

（3）创建自定义管理员（用户名：xiaodao，密码：123）

```bash
INSERT INTO ofUser (username, plainPassword, encryptedPassword, name, email, creationDate, modificationDate) VALUES 
('xiaodao','123','123','Administrator','xiaodao@sunyard.com','0','0');
```

  注意：如果重设了用户名，必须重启openfire服务器

 ![](/assets/images/2014/20140114_1/13.png)


#### 8.后台控制界面

 ![](/assets/images/2014/20140114_1/14.png)


### 三、卸载openfire

#### 1.停止服务

在系统偏好设置的其他里，打开openfire偏好设置

![](/assets/images/2014/20140114_1/15.png)

点击Stop Openfire按钮，停止服务

![](/assets/images/2014/20140114_1/16.png)


#### 2.删除文件

打开终端，输入以下命令

```bash
sudo rm -rf /Library/PreferencePanes/Openfire.prefPane
sudo rm -rf /usr/local/openfire
sudo rm -rf /Library/LaunchDaemons/org.jivesoftware.openfire.plist
```

其中第一条命令之后，需要输入本机管理员密码
