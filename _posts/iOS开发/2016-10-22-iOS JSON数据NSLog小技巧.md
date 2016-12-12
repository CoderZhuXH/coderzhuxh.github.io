---
layout: post
title: iOS JSON数据NSLog小技巧
categories: iOS
description: iOS 技巧之谈。
keywords: JSON, 技巧, NSLog
---

###一.前言
说到JSON数据NSLog输出,大家可能并不陌生,在向服务器数据请求时,会经常用到,但Xcode的JSON输出并不怎么美丽,
*	1.字段""缺失
*	2.数组输出为( )
*	3.unicode编码中文没有自动显示为中文
*	4.大部分情况下copy打印的JSON数据到JSON在线格式化工具,都报错,格式化不了
*      5.其实有些第三方插件也可以解决这个问题,现在给大家分享一个比较简单的解决方法,几行代码的扩展就可以解决这个问题.

###二.先看效果
####1.Xocde JSON数据正常NSLog输出,和copy到JOSN在线格式化工具情况如下:
![image](http://upload-images.jianshu.io/upload_images/2229730-d9bb01214aa6065a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####2.添加扩展后.NSLog输出和JSON数据格式化效果如下
![image](http://upload-images.jianshu.io/upload_images/2229730-c3d0541fa455e1a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####3.效果很明显了:
*	1.自动补全字段缺失""
*	2.自动转换数组( )为[ ]
*	3.自动转换unicode编码为中文
*	4.能正常进行JSON数据格式化
*      5.自动转换,NSLog不用添加任何更改.

###三.好了,不多说,直接上代码
#####1.新建一个NSdictionasy 的Category,笔者取名为NSDictionary+XHLogHelper,如下:
![image](http://upload-images.jianshu.io/upload_images/2229730-af6ff693438af981.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2229730-8df81b9d5606cbc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####2.打开 NSDictionary+XHLogHelper.m 文件,添加代码如下:
```objc
#import "NSDictionary+XHLogHelper.h"

@implementation NSDictionary (XHLogHelper)

#if DEBUG
- (NSString *)descriptionWithLocale:(nullable id)locale{

  NSString *logString;

    @try {

        logString=[[NSString alloc] initWithData:[NSJSONSerialization dataWithJSONObject:self options:NSJSONWritingPrettyPrinted error:nil] encoding:NSUTF8StringEncoding];

    } @catch (NSException *exception) {

        NSString *reason = [NSString stringWithFormat:@"reason:%@",exception.reason];
        logString = [NSString stringWithFormat:@"转换失败:\n%@,\n转换终止,输出如下:\n%@",reason,self.description];
   
 } @finally {
 
    }
        return logString;
}
#endif

@end

```
#####3.大功告成了
*    上面代码作用是,DEBUG模式下,NSLog输出字典时,转换为JSON字符串的形式输出,转换失败,按原始数据输出,`- (NSString *)descriptionWithLocale:(nullable id)locale`方法我就不解释了,有兴趣同学可百度了解下.
*    此方法转JSON字典不会出现失败情况,什么情况下会转换失败按原始数据输出呢(比如自定义字典键值对中有NSData,NSDate等类型数据)
*    现在你可以在工程任何一个地方NSLog JSON数据,输出是不是已经转换为标准JSON格式了.

#####4.Tips
*  项目开发中大家可以自定义一个只在DEBUG模式下才打印DebugLog,所有调试输出都用这个打印,可以减少在应用打包上线后,后台打印造成的性能浪费,代码如下:
```objc
#ifdef DEBUG
#define DebugLog(...) NSLog(__VA_ARGS__)
#else
#define DebugLog(...)
#endif
```

###四.小结:
*    1.此方法很适合NSLog JSON数据,因为可以直接在线格式化,看复杂JSON数据很方便   
*    2.上面代码已上传到了Github,有需要同学可以直接下载,添加到项目中.Github代码地址:https://github.com/CoderZhuXH/XHLogHelper