---
layout: post
title: XCode5.0.2免证书真机调试（通用）
date: 2014-02-07 10:15:33.000000000 +08:00
tags: 转载 & 收藏
---

* 原文地址：http://www.cnblogs.com/wengzilin/p/3441116.html
* 感谢 [编程小翁](http://www.cnblogs.com/wengzilin/)


### 理论上之后的XCode版本也可以通过这种方法破解



唉，证书到期了，申请很麻烦，好吧，破解xcode+越狱iphone继续折腾。。。

开发环境：MAC 10.9.1、Xcode5.0.2、iphone5(IOS7.0.4)

#### 友情提示：在修改文件之前请先备份原文件，一旦误操作导致Xcode出问题了还原回去就好


想要免证书真机调试必须牢记以下的准则：

	准则1：设备必须先越狱，而且用cydia装好appSync补丁

	准则2：在前期操作过程中，xcode5必须保持完全关闭状态，否则有些变化无法更改

方法与低版本的xcode实现方法大同小异。

Let`s go.


### 1、创建证书：

利用mac的实用工具钥匙串创建，选项严格按照如下填写，剩下的就是下一步下一步，邮箱该填的填，不填也没事

![](/assets/images/2014/20140207/1.png)


### 2.直接双击/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS7.sdk 此目录下的SDKSettings.plist

将以下两段中的YES改为NO

![](/assets/images/2014/20140207/2.png)

若遇到无法修改的情况，将文件复制出来，修改完再替换原文件即可。


### 3. 双击/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/下的Info.plist

把全部的XCiPhoneOSCodeSignContext 修改成 XCCodeSignContext，有三个地方出现，可以按cmd+f查找。

![](/assets/images/2014/20140207/3.png)

修改完，右击­­Add Row,增加两项: PROVISIONING_PROFILE_ALLOWED 值为 NO ，PROVISIONING_PROFILE_REQUIRED 值为 NO。

同样，若遇到无法修改的情况，将文件复制出来，修改完再替换原文件即可。


### 4. （低版本xcode有介绍，但我没这么做，可以先跳过，后面报错了再回来补这个步骤）打个二进制补丁。

```bash
cd ~/Desktop
vim script
```

打上下面内容:

```bash
#!/bin/bash cd /Developer/Platforms/iPhoneOS.platform/Developer/Library/Xcode/PrivatePlugIns/iPhoneOS\ Build\ System\ Support.xcplugin/Contents/MacOS/ dd if=iPhoneOS\ Build\ System\ Support of=working bs=500 count=255 printf "xc3x26x00x00" >> working /bin/mv -n iPhoneOS\ Build\ System\ Support iPhoneOS\ Build\ System\ Support.original /bin/mv working iPhoneOS\ Build\ System\ Support chmod a+x iPhoneOS\ Build\ System\ Support
```

授予这个脚本执行权限并执行它

```bash
chmod 777 script
./script
```

正常的话应该输出(具体的数字可能有差别)

```bash
231+1 records in
231+1 records out
115904 bytes transferred in 0.001738 secs (66694555 bytes/sec)
```


#### 经实际操作发现如下步骤虽然可以将程序安装到设备，也可以运行，但是无法进行联机调试，会报failed to get the task for process xxx错误，如果必须要联机调试看Log请跳到文章最后。



### 5、准备脚本，为后面做准备，把下面的命令行在联网的情况下一行一行执行：权限不够的话先进入sudo -s 进入超级管理员权限

```bash
mkdir /Applications/Xcode.app/Contents/Developer/iphoneentitlements 
cd /Applications/Xcode.app/Contents/Developer/iphoneentitlements 
curl -O http://www.alexwhittemore.com/iphone/gen_entitlements.txt 
mv gen_entitlements.txt gen_entitlements.py 
chmod 777 gen_entitlements.py
```

最后一句意思是将该脚本文件设为可执行。


上面的命令执行成功之后，会在/Applications/Xcode.app/Contents/Developer/目录下生成一个iphoneentitlements文件夹和其下的gen_entitlements.py文 件，如果你的电脑没有联网或者不能自动生成相关目录文件，那么需要手动在相应的目录创建指定的文件，随后需要给gen_entitlements.py设置权限。

gen_entitlements.py脚本文件的内容如下：

```bash
#!/usr/bin/envpython
import sys
import struct
if len(sys.argv)!= 3:

print "Usage: %s appnamedest_file.xcent" % sys.argv[0]

sys.exit(-1)

APPNAME =sys.argv[1]

DEST =sys.argv[2]

if notDEST.endswith('.xml') and not DEST.endswith('.xcent'):

    print "Dest must be .xml (for ldid) or.xcent (for codesign)"
    sys.exit(-1)

entitlements ="""

<?xmlversion="1.0" encoding="UTF-8"?>
<!DOCTYPEplist PUBLIC "-//Apple//DTD PLIST 1.0//EN""http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plistversion="1.0">
<dict>
    <key>application-identifier</key>
    <string>%s</string>
    <key>get-task-allow</key>
    <true/>
</dict>
</plist>
"""% APPNAME

f = open(DEST,'w')
ifDEST.endswith('.xcent'):
    f.write("\xfa\xde\x71\x71")
    f.write(struct.pack('>L',len(entitlements) + 8))
f.write(entitlements)
f.close()
```

将该脚本文件设为可执行：

```bash
sudo chmod 777 /Applications/Xcode.app/Contents/Developer/iphoneentitlements/gen_entitlements.py
```


### 6、以下的步骤每个想真机调试的工程都要执行！

![](/assets/images/2014/20140207/4.png)


### 7、添加自定义的脚本，这一步将会让xcode执行上一步的脚本文件：

xcode5之前版本添加自定义的脚本，是在Build Phases中添加一个Phase，右下角的Add Build Phase，然后单击Add Run Script添加脚本；

而xcode5则是从菜单行上选Editor-Add Build Phase，然后单击Add Run Script添加脚本。

脚本内容如下，直接复制粘贴即可：

```bash
export
CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/codesign_allocate
if [ "${PLATFORM_NAME}" =="iphoneos" ] || [ "${PLATFORM_NAME}" == "ipados"]; then
/Applications/Xcode.app/Contents/Developer/iphoneentitlements/gen_entitlements.py "my.company.${PROJECT_NAME}" "${BUILT_PRODUCTS_DIR}/${WRAPPER_NAME}/${PROJECT_NAME}.xcent";

codesign -f -s "iPhone Developer" --entitlements "${BUILT_PRODUCTS_DIR}/${WRAPPER_NAME}/${PROJECT_NAME}.xcent" "${BUILT_PRODUCTS_DIR}/${WRAPPER_NAME}/"

fi
```


### 8、连上设备，到xcode->organizer中看设备是否是绿灯。然后点击运行，ok


===============================================================================


由于网上教程很多，以上内容很多都是直接复制粘贴过来的 ，我在实际操作中发现在真机调试时会报failed to get the task for process xxx错误，虽然程序可以安装到设备但是无法联机调试看Log，经过多次尝试发现可以通过如下方法解决：


#### 1.在工程中新建一个名为Entitlements.plist的文件

步骤：

New->File->iOS->Resouce->Property List

将文件名设为Entitlements.plist


#### 2.打开Entitlements.plist文件

添加一个属性Can be debugged,并将属性值设为YES。

![](/assets/images/2014/20140207/5.png)


#### 3.修改targets的build setting属性值。

将Code Signing Entitlements 那项的值改为刚刚新建得”Entitlements.plist”

将Code Signing Identity中Any iOS SDK设置为iphone Developer，其他则改为Don’t Code Sign.(此处要和证书里面那个名字要吻合)

![](/assets/images/2014/20140207/6.png)


此处需要注意一点，Code Signing Entitlements处不要直接写Entitlements.plist，而是要写全路径，不然会报错找不到这个文件，之后就可以无证调试了，爽！
