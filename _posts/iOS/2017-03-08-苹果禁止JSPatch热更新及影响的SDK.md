---
layout: post
title: 苹果禁止JSPatch热更新及影响的SDK
categories: Objective-C
description: iOS开发
keywords: iOS,JSPatch,OC,热更新

---


![WWDC.jpeg](http://upload-images.jianshu.io/upload_images/2229730-181a03981e57e5f0.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###一.今天一早不少iOS开发者收到了苹果的警告邮件,内容如下:

>Your app, extension, and/or linked framework appears to contain code designed explicitly with the capability to change your app’s behavior or functionality after App Review approval, which is not in compliance with section 3.3.2 of the Apple Developer Program License Agreement and App Store Review Guideline 2.5.2. This code, combined with a remote resource, can facilitate significant changes to your app’s behavior compared to when it was initially reviewed for the App Store. While you may not be using this functionality currently, it has the potential to load private frameworks, private methods, and enable future feature changes. 

>This includes any code which passes arbitrary parameters to dynamic methods such as dlopen(), dlsym(), respondsToSelector:, performSelector:, method_exchangeImplementations(), and running remote scripts in order to change app behavior or call SPI, based on the contents of the downloaded script. Even if the remote resource is not intentionally malicious, it could easily be hijacked via a Man In The Middle (MiTM) attack, which can pose a serious security vulnerability to users of your app.

>Please perform an in-depth review of your app and remove any code, frameworks, or SDKs that fall in line with the functionality described above before submitting the next update for your app for review.

>Best regards

>App store Review

###二.苹果审核协议中有这样二项

![审核.png](http://upload-images.jianshu.io/upload_images/2229730-dfbaa968c0dc85d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于开发者APP中集成或SDK中包含了热更新功能,受到苹果警告.

热更新技术可以帮开发者避免长时间的审核等待以及多次被拒造成的成本开销,以及快速修复线上Bug,但也给黑客留了后门，也就违反了苹果的安全和隐私政策.

从苹果的角度看，禁止应用使用热更新技术更多是为了保护用户隐私、数据安全以及其全力打造的生态圈.

作为开发者,苹果爸爸开始抓这一块,我们还是老老实实去除应用热更新功能吧

###三.目前已知有高德地图、个推、BugTags、Bugly含有热更新功能

####1.高德地图

高德地图已在第一时间修复并更新了SDK,
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2229730-58dd51ac83b9be7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####2.个推

个推暂时还没有推出更新.
但可咨询技术支持,
提供去除JSPatch临时包解决问题,
官方将在下周一(3.13)更新SDK解决该问题

####3.BugTags

BugTags 本身没有直接集成JSPatch,只有动态监测应用是否集成了JSPatch,但依然会陪apple 认定为包含动态更新功能,
官方已紧急发布SDK v2.2.1版本移除JSPacth相关代码
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2229730-4c04b2c54eb06d66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####4.Bugly

官方提示:
由于 Apple 审核政策调整，我们正在关注是否影响热更新 SDK 功能。

####JSPatch

JSPatch issue区炸锅了<https://github.com/bang590/JSPatch/issues/746>

###四.最后来个段子(摘自网络)
![摘自网络.png](http://upload-images.jianshu.io/upload_images/2229730-7ccbd6ff5508067a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
祝愿所有开发者都能顺利通过审核并上线.