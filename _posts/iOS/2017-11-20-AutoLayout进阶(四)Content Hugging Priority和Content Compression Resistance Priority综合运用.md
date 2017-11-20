---
layout: post
title: AutoLayout进阶(四)Content Hugging Priority和Content Compression Resistance Priority综合运用
categories: Objective-C
description: iOS开发
keywords: iOS, AutoLayout , Content Hugging Priority , Content Compression Resistance Priority

---

前面两个章节给大家详细介绍了Content Hugging Priority(抗拉伸优先级)和Content Compression Resistance Priority(抗压缩优先级),
本文将综合运用这两个属性,来处理一个项目中的实际需求.

![](http://upload-images.jianshu.io/upload_images/2229730-e9c6ad66447fc5c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  一.  要点回顾:
#####  1.Content Hugging Priority
视图抗拉伸优先级,
值越小,视图越容易被拉伸,
此值默认251

#####  2.  Content Compression Resistance Priority:
视图抗压缩优先级,
值越小,视图越容易被压缩,
此值默认为750

####  二.实际运用

我先来看下下面这个效果,在不计算文字宽度和不修改约束的前提下,怎么通过设置Content Hugging Priority和Content Compression Resistance Priority属性来实现:

![Demo.gif](http://upload-images.jianshu.io/upload_images/2229730-ef5dffff64c420e5.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

效果描述:

*  这个是社区评论页面里,用户信息的一部分
*  最左边是:用户图像
*  黄色Label:用户昵称(昵称长度不确定)
*  蓝色Label:评论发表时间(时间长度不确定)

两点要求:

*  1.黄色abel(显示昵称),其宽度由用户昵称长度决定,
    蓝色label(显示时间),其宽度由时间长度决定,
*  2.当用户昵称长度变长时,蓝色label自动右移,移到屏幕边缘时,用户昵称继续增加,昵称将缩略显示
    3.当用户昵称变短时,蓝色label自动左移.

####  三.代码示例
新建工程,在页面添加一个view,并添加约束固定位置和大小,
在这个view上添加三个视图,分别为:

1.imageView,用来显示用户图像
2.黄色label,用来显示用户昵称
3.蓝色label,用来显示时间

并添加如下约束:

```objc
imageView:上0,左0,下0,长宽比1:1
黄色label:上0,左0,下0,
蓝色label:上0,左0,下0,右0

黄色label和蓝色label都不添加宽度约束,其宽度有文字长度决定
```
添加完约束后,会报下面错误:

![Demo.png](http://upload-images.jianshu.io/upload_images/2229730-c4ff47b3950006f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此错误的意思是:

*  黄色label,和蓝色label都是动态宽度(没添加宽度约束),
*  当二者宽度之和,大于右侧区域总宽度时,AutoLayout不知道先压缩哪个label,
*  当二者宽度之和,小于右侧区域总宽度时,AutoLayout不知道先拉伸哪个label,
*  让我们为二者添加Content Hugging Priority和Content Compression Resistance Priority约束,

下面来添加这两个约束:

*  1.很显然,当用户昵称过长时,我们希望过长的部分省略显示(即昵称过长时,黄色label优先被压缩,其横向抗压缩优先级要低)
*  2.当用户昵称太短时,我们希望蓝色label向左侧靠过来.(即昵称太短时,蓝色label向左靠,要被拉长,其横向抗拉伸优先级要低)

下面我们来为黄色label的Content Compression Resistance Priority(抗压缩优先级)横向优先级为749,如下:

![黄色label.png](http://upload-images.jianshu.io/upload_images/2229730-6fbeaa427f73e3ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改蓝色label的Content Hugging Priority(抗拉伸优先级)横向优先级为250,如下:

![蓝色label.png](http://upload-images.jianshu.io/upload_images/2229730-77bf5877d876381e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加完毕后,我们右键拖线两个label生成变量,
并起一个定时器,
依次设置黄色label文字为:
```objc
长昵称这是一个很长的昵称
长昵称这是一个很长的昵
长昵称这是一个很长的
长昵称这是一个很长
长昵称这是一个很
长昵称这是一个
长昵称这是一
长昵称这是
长昵称这
长昵称
长昵称这
长昵称这是
长昵称这是一
长昵称这是一个
长昵称这是一个很
长昵称这是一个很长
长昵称这是一个很长的
长昵称这是一个很长的昵
长昵称这是一个很长的昵称
```
不需要计算文字宽度,不需要修改约束,就达到上图效果.

代码如下:

```objc

#import "ViewController.h"

static NSString *const NameText = @"长昵称这是一个很长的昵称";
static NSInteger changeLength = -1;//记录单次变化长度

@interface ViewController ()
@property (weak, nonatomic) IBOutlet UILabel *nameLab;
@property (weak, nonatomic) IBOutlet UILabel *timeLab;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    _nameLab.text = NameText;//初始值
    _timeLab.text = @"一周以前";//初始值

    [NSTimer scheduledTimerWithTimeInterval:0.1 target:self selector:@selector(action) userInfo:nil repeats:YES];
}

-(void)action{
     /** 当前昵称 */
    NSString *name = [NameText substringToIndex:_nameLab.text.length+changeLength];
    NSLog(@"当前昵称:\n%@",name);
    
    _nameLab.text = name;//设置昵称
    
    if(_nameLab.text.length<=3){//最小宽度
        changeLength = 1;//加长
    }else if(_nameLab.text.length==NameText.length){//最大宽度
        changeLength = -1;//减短
    }
}


- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

@end

```

####  三.小结:

Content Hugging Priority 视图抗拉伸优先级
值越小,越先被拉伸

Content Compression Resistance 抗压缩优先级
值越小,越先被压缩,

这两个属性,在UITableViewCell 自动高度上有这广泛运用,下一章节将重点来将,复杂TableViewCell,在不计算子文字高度情况下,不用第三方TableViewCell自动计算高度框架情况下,怎么实现自动高度.

----------------------
代码地址:<https://github.com/CoderZhuXH/AutoLayout>


-转载注明出处