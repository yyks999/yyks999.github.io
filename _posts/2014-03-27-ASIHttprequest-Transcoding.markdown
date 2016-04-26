---
layout: post
title: Cocoapods 常见问题解决方案
date: 2014-03-27 14:31:51.000000000 +08:00
tags: 问题 & 原创
---


* 当我们进行网络请求的时候，有时返回的数据总会出现乱码，无论你进不进行utf-8转码都没有任何效果。这时我们需要转换下方式。
* 我们可以先将它转为NSData，然后再转成NSString，这时候再打印出来，你可能回发现，乱码问题已经解决。
* 代码如下：

```bash
NSData *jsondata = [request responseData];
NSString *jsonString = [[NSString alloc] initWithBytes:[jsondata bytes] length:[jsondata length] encoding:NSUTF8StringEncoding];
```
