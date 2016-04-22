---
layout: post
title: iBeacon开发
date: 2014-02-28 10:01:12.000000000 +08:00
tags: 转载 & 收藏
---

* 原文地址：http://esoftmobile.com/2013/12/15/ibeacons/
* 感谢 [TracyYih](http://weibo.com/534072785?s=6cm7D0)


![](/assets/images/2014/20140228/1.png)


### 什么是iBeacon

* * *

iBeacon是苹果在2013年WWDC上推出一项基于蓝牙4.0（Bluetooth LE | BLE | Bluetooth Smart）的精准微定位技术，当你的手持设备靠近一个Beacon基站时，设备就能够感应到Beacon信号，范围可以从几毫米到50米。iBeacon相比较于原来的蓝牙技术有几个特点：
首先它不需要配对，所以你不用担心一个名为『一头母猪』的蓝牙设备请求和你配对^_^。苹果在之前对蓝牙设备的控制比较严格，所以只有通过MFI认证过的蓝牙设备才能与iDevice连接，而蓝牙4.0就没有这些限制了；
准确与距离。普通的蓝牙（蓝牙4.0之前）一般的传输距离在0.1~10m，而iBeacon信号据说可以精确到毫米级别，并且最大可支持到50m的范围；
功耗更低。其实蓝牙4.0又叫低功耗蓝牙，一个普通的纽扣电池可供一个Beacon基站硬件使用两年。
目前已经有不少硬件厂商都在生产Beacon发射硬件，文章配图为[Estimote](http://estimote.com/)公司生产的宝石形状的Beacon。当然并不是非得购买这些Beacon硬件才能使用iBeacon技术，其实从iPhone 4S和iPad 3及后续设备都已经支持蓝牙4.0，所以这些设备升级到iOS7都能够支持iBeacon，同时也能作为Beacon发射基站使用。 苹果在全美254家Apple Store中部署iBeacon很多就是直接使用iDevice作为基站。


### Passbook + iBeacon

* * *

在iOS7中，Passbook的功能所有增强，当然也少不了对iBeacon的支持，你只需要在pass.json文件中加入beacons字段，然后填写上与该Pass相关的beacon基站信息，包括proximityUUID、major、minor以及当该Pass接收到该beacon信号时需要显示的文本relevantText。这样，当你把这个包含beacons信息的Pass加入到Passbook，并靠近beacons中的某个基站时，该Pass的信息就会自动出现在手机的锁屏界面上，并显示relevantText中的文本。当然得有一个前提：手机打开蓝牙。

```objc
  "beacons":[
    {
     "proximityUUID" : "E2C56DB5-DFFB-48D2-B060-D0F5A71096E0",
     "relevantText" : "TechDay 2013 Beijing",
     "major" : 0,
     "minor" : 0
     }
  ],
```

和 locations 字段一样，一个Pass文件中最多支持10个beacon基站信息。其实这样做也是出于省电考虑，因为系统在每次接收到beacon信号时，都会在Passbook库中轮询每一个Pass的beacons信息，匹配后才将它显示出来，所以如果不做数量限制，耗电量可能就难以接受，locations原理也类似。


### iBeacon开发

* * *

#### Beacon Monitoring

因为是一种定位技术，苹果将iBeacon相关的接口放到了 CoreLocation.framework 。在iOS7之前，我们可以通过CLRegion定义一个地理区域，来跟踪设备在该区域内的运动情况，iOS7之后，CLRegion被完全变成了一个抽象类，子类CLCircularRegion和CLBeaconRegion分别承担实现一个地理区域和Beacon信号区域的功能。
即iOS7之后的CLRegion主要有两个属性：

```objc
@interface CLRegion : NSObject <NSCopying, NSSecureCoding>
@property (nonatomic, assign) BOOL notifyOnEntry;
@property (nonatomic, assign) BOOL notifyOnExit;
@end
```

notifyOnEntry和notifyOnExit分别标记是否在进入和离开该区域时对是否获得通知（代理方法）。CLBeaconRegion另外增加了一个属性notifyEntryStateOnDisplay标记是否在用户手机屏幕点亮时获得通知。
我们要监听一个beacon基站，需要创建一个对应基站的区域信息CLBeaconRegion，并设置相应的监听属性：

```objc
NSUUID *uuid = [[NSUUID alloc] initWithUUIDString:@"E2C56DB5-DFFB-48D2-B060-D0F5A71096E0"];
CLBeaconRegion *targetBeaconRegion = [[CLBeaconRegion alloc] initWithProximityUUID:uuid identifier:@"TechDay 2013 Beijing"];
targetBeaconRegion.notifyOnEntry = YES;
targetBeaconRegion.notifyOnExit = YES;
targetBeaconRegion.notifyEntryStateOnDisplay = YES;
```

创建完Regoin后，我们需要对该Region进行监控以获取是否进入该区域及一些距离等信息，创建一个CLLocationManager实例然后调用startMonitoringForRegion:方法来监控上面的BeaconRegion：

```objc
self.locationManager = [[CLLocationManager alloc] init];
self.locationManager.delegate = self;
[self.locationManager startMonitoringForRegion:targetBeaconRegion];
```

剩下的就是通过CLLocationManagerDelegate中的各个方法来接收所监听基站的信息，如进入或离开该Beacon区域，计算举例某个CLBeacon的距离等。
需要说明一点的是，CLLocationManager默认是可以在程序退到后台后继续监听的，也就是只要设置了notifyOnEntry、 notifyOnExit、 notifyEntryStateOnDisplay这几个属性，程序退到后台时如果监测到beacon信息（进入、离开、屏幕点亮）时，会通知到代理方法：- (void)locationManager:(CLLocationManager *)manager didDetermineState:(CLRegionState)state forRegion:(CLRegion *)region，你可以在该代理里面进行一些信息处理或推送一个本地消息提示用户，用户查看该消息会将程序调用起来。
在iOS7.1中，苹果对iBeacon功能进行了加强，不用程序保持在后台了，哪怕程序强制关掉（双击Home键后滑掉）、手机重启，只要设备监测到了对用的Beacon信息，在设备屏幕点亮时一样会调用上面的代理通知程序，这无疑增加了iBeacon的应用场景。


#### Beacon Broadcasting

前面我们说到所有支持蓝牙4.0的iDevice都能够作为beacon基站发射信号，这就需要 CoreBluetooth.framework 的支持。 我们需要创建一个CBPeripheralManager实例，然后发射beacon广播信号：

```objc
//为beacon基站创建一个唯一标示
NSUUID *myUUID = [[NSUUID alloc] initWithUUIDString:@"A4E86DC5-A0E2-G7W0-B060-A0F5A71096C0"];
CLBeaconRegion *myBeaconRegion = [[CLBeaconRegion alloc] initWithProximityUUID:myUUID identifier:@"iBeacon"];

//获取该Beacon区域的信号信息
NSDictionary *peripheralData = [myBeaconRegion peripheralDataWithMeasuredPower:nil];

//创建并广播Beacon信号
CBPeripheralManager *peripheralManager = [[CBPeripheralManager alloc] initWithDelegate:self queue:dispatch_get_main_queue()];
[peripheralManager startAdvertising:peripheralData];
```

当然你还需要在CBPeripheralManagerDelegate代理方法：peripheralManagerDidUpdateState:根据不同的状态做一些处理。


### 总结

* * *

苹果的伟大之处就是在于将复杂的技术以简单的形式呈现出来，相信看完本文你已经对iBeacon开发相关的技术有了很好的了解，然而iBeacon技术本身的应用才是真正体现价值的地方，相信它能给很多行业带来变革。

* * *

推荐官方示例代码：[AirLocate](https://developer.apple.com/library/ios/samplecode/AirLocate/Introduction/Intro.html)

