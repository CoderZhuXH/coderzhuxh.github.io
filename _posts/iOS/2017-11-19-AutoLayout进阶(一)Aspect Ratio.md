---
layout: post
title: AutoLayout进阶(一)Aspect Ratio
categories: Objective-C
description: iOS开发
keywords: iOS, AutoLayout , Aspect Ratio

---

AutoLayout相信大家都不陌生,iOS开发中涉及到UI布局的地方,都有它的身影,基本用法很多同学都会,本文就不做介绍,下面就AutoLayout一些进阶高级用法,做详细介绍,掌握好这些技巧,iOS开发将事半功倍,由于篇幅较长,将拆分为几个篇幅,一一介绍.

![](http://upload-images.jianshu.io/upload_images/2229730-ac4a8a29f1ab55d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




####  一.  本篇重点介绍:Aspect Ratio

#####  1.  Aspect Ratio:
设置视图的宽高比

#####  2.  使用场景:
视图宽度随着屏幕宽度变化拉伸时,让其高度自动进行等比例拉伸.保持该视图宽高比不变.

#####  3.  Aspect Ratio在约束界面如下位置:
![Aspect Ratio 位置.png](http://upload-images.jianshu.io/upload_images/2229730-b51f20efdc74b762.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
####  二.  Demo效果:
![Demo.gif](http://upload-images.jianshu.io/upload_images/2229730-e6b7da0efc07af65.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  三.  代码示例:
1.新建工程,在视图控制器中添加一个 imageView,并为其设置一张宽高比为16:9的图片.

2.对imageView添加如下约束.

```objc
1.竖向居中
2.增加宽度约束为320
3.设置Aspect Ratio宽高比为16:9
4.增加顶部约束
```
![约束.png](http://upload-images.jianshu.io/upload_images/2229730-59fef2fceb30293f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.分别选中imageView、宽度约束右键脱线生成变量imgView、imgViewWidth
起一个定时器,动态修改其宽度imgViewWidth属性,代码如下:

```objc

#import "ViewController.h"

 static CGFloat changeValue = -18;//记录变化值

@interface ViewController ()
@property (weak, nonatomic) IBOutlet UIImageView *imgView;
@property (weak, nonatomic) IBOutlet NSLayoutConstraint *imgViewWidth;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    [NSTimer scheduledTimerWithTimeInterval:0.1 target:self selector:@selector(action) userInfo:nil repeats:YES];
}

-(void)action{
    _imgViewWidth.constant += changeValue;
    if(_imgViewWidth.constant<=150){//最小宽度
        changeValue = 18;
    }else if(_imgViewWidth.constant>320){//最大宽度
        changeValue = -18;;
    }
}

@end

```


####  四.小结:
由于设置了imgView宽高比为16:9,所以宽度动态变化时,其高度也会根据设置的宽高比做相应变化.

----------------------
代码地址:<https://github.com/CoderZhuXH/AutoLayout>

-转载注明出处