---
layout: post
title: MAC下安装与配置MySQL
date: 2014-01-14 15:32:24.000000000 +09:00
---

原味地址：http://www.cnblogs.com/macro-cheng/archive/2011/10/25/mysql-001.html
感谢MacroCheng

### 一 下载MySQL

访问MySQL的官网http://www.mysql.com/downloads/ 然后在页面中会看到“MySQL Community Server”下方有一个“download”点击。

![](/assets/images/2014/20140114/1.png)

进入MySQL的下载界面（http://www.mysql.com/downloads/mysql/），如果你是用的Mac OS来访问的话那么就会默认为你选好了Mac OS X 平台，而下面罗列的都是在Mac OS上能用的MySQL的版本，如果是用的其他平台，在“Select Platform”选项的下拉列表中选一下就好了。

在Mac OS上的MySQL的版本很多，其中有按平台来的，比如10.5/10.6等平台，然后有32位的和64位的，这个你按照自己系统的情况来进行选择，然后 就是文件的后缀名有.tar.gz的和.dmg的，这里我选择的是.dmg的。点击右侧的download进行下载。

![](/assets/images/2014/20140114/2.png)

然后会跳转到另外一个界面，这个界面是提示你需不需要注册的，直接选择最下面的“No thanks,just take me to downloads!”,然后这才真正跳转到了下载的界面，这个界面列了很多的供下载的服务器，选择一个服务器进行下载就OK了。



### 二 安装MySQL

打开MySQL的安装包：

![](/assets/images/2014/20140114/3.png)

分别安装

mysql-5.5.16-osx10.6-x86_64.pkg：这个是MySql的主要程序包;

MySQL_StartupItem.pkg：MySql的启动项;

MySQL.prefPane：安装完成后会在系统的偏好设置里面出现，是MySQL的偏好设置，里面主要是用来启动MySQL服务的.



### 三 下载安装MySQL Workbench(GUI Tool)

#### 3.1 MySQL Workbench的下载

访问http://www.mysql.com/downloads/ 在下面有一个MySQL Workbench(GUI Tool)的项，点击其下的DOWNLOAD即可进入下载界面：

![](/assets/images/2014/20140114/4.png)

然后同样选择版本之后选择服务器进行下载。这里貌似只有一个版本：

![](/assets/images/2014/20140114/5.png)

#### 3.2 MySQL Workbench的安装

下载完成之后安装就非常简单，双击即可安装。安装完成之后我们在“应用程序”里面就能看到MySQL Workbench.app程序了。双击打开：

![](/assets/images/2014/20140114/6.png)

到这里MySql Workbench就安装完毕了。

安装完成之后我们就讲MySQL Workbench连接到MySQL的数据库上，然后对数据库进行管理。

#### 3.3 建立一个新连接

可以看到MySQL Workbench的主界面有三大模块，SQL Development，Data Modeling，Server Administration。其中在SQL Development下面有一个“New Connection”点击之后就会出现一个“Setup New Connection”的对话框,填写完Connetion Name之后点击Ok。即可完成一个连接到本地数据库的连接。

![](/assets/images/2014/20140114/7.png)

完成之后在主界面的就会出现刚才建立的连接，如下图：

![](/assets/images/2014/20140114/8.png)

双击连接名或者选中一个连接之后点击“Open Connection to Start Querying”，即可进入这个操作数据库的界面：

![](/assets/images/2014/20140114/9.png)

这些所有的前提都是数据库服务得打开。

#### 3.4 管理数据库的访问密码

MySQL的默认账号密码是root/root，正常情况下我们如果单纯的只是使用MySQL Workbench来管理数据库的这个账号是可以的，但是当我们在编程代码中通过jdbc来访问MySQL时我们就会发现使用这个账号是不行，无法访问， 因为MySQL需要我们更改密码，也就是说root这个是个默认的密码也就是弱密码，需要我们修改之后才能在代码中使用。因此我们需要来管理数据库的访问 密码。

新建一个Server Instance

在“Server Administration”模块下有个“New Server Instance”点击之后会弹出一个“Create New Server Instance Profile”的对话框，跟着对话框的一步一步走就可以完成，一般本地的数据库直接跟着默认设置就ok。完成之后我们就能够在Workbench的主界 面最右边看到刚才建立的instance。

![](/assets/images/2014/20140114/10.png)

双击打开管理器，这里需要密码，一般还没改过的就是root。然后在左侧的菜单栏下有个“SECURITY”下面有个“Users and Privileges”的子菜单项，选择就会看到如下界面：

![](/assets/images/2014/20140114/11.png)

在右侧的面板中有一个User Accounts的列表，选择其中要修改密码的账号，然后在右侧修改密码即可。