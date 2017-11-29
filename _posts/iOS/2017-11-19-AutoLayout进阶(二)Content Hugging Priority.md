---
layout: post
title: AutoLayout进阶(二)Content Hugging Priority
categories: Objective-C
description: iOS开发
keywords: iOS, AutoLayout , Content Hugging Priority

---


本文将给大家介绍AutoLayout的Content Hugging Priority,说通俗一点就是"视图的抗拉伸优先级".
说到Content Hugging Priority不得不提Content Compression Resistance(抗压缩优先级),我会在下一篇文章详细来介绍它


![](http://upload-images.jianshu.io/upload_images/2229730-009eeb54afbbd317.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  一.  本篇重点介绍:Content Hugging Priority
许多开发者可能对这个属性比较陌生,这个属性对有intrinsic content size的控件（例如button，label）非常重要.
注:具有intrinsic content size的控件当你设置内容后,它可以根据内容多少来调整自己大小.

#####  1.  Content Hugging Priority:
抗拉伸优先级,
值越小,视图越容易被拉伸


#####  2.  使用场景:
当一个视图上有多个intrinsic content size的控件动态尺寸,在这几个视图内容总和,不够填充父视图区域时,此属性可以控制优先拉伸哪个视图内容.

#####  3.  Content Hugging Priority在约束界面如下位置:
![Content Hugging Priority.png](http://upload-images.jianshu.io/upload_images/2229730-1682d54e46896980.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  二.  代码示例:
1.新建工程,我们添加一个固定大小的视图view,在这个view上放2个label,分别为红绿,分别对其添加如下约束:
```objc
红label约束:上0,左0,右0
绿label约束:上0,左0,右0,下0
```
均不给高度约束,高度由label文字多少自己决定,
此时你会发现,添加完以上约束后,会报下面错误:

![error.png](http://upload-images.jianshu.io/upload_images/2229730-66bc2b35b0dbcc01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这个错误的意思是说:
*  这2个label都是动态高度(未添加高度约束),
*  当2个label总高度小于父view高度时,AutoLayout不知道优先拉伸哪个label.
*  当2个label总高度大于父view高度时,AutoLayout不知道优先压缩哪个label.
*  让我们添加Content Hugging Priority抗拉伸和Content Compression Resistance抗压缩优先级.

`注:本篇文章主要讲Content Hugging Priority抗拉伸优先级,
Content Compression Resistance抗压缩优先级将放在下一篇文章来详细介绍)`

下面我们依次修改这2个label 纵向Content Hugging Priority值分别为:
```objc
红label: vertical: 252
绿label: vertical: 251
```

添加完成后错误消失,效果如下:
![红-252,绿-251.png](http://upload-images.jianshu.io/upload_images/2229730-56f18b165d066c00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为绿色label纵向抗拉伸优先级为251,小于红色label的值252,所有当2个label内容不够填充俯视图区域时,绿色label高度被拉伸了

下面我们来调换2个label纵向拉伸优先级:
```objc
红label: vertical: 251
绿label: vertical: 252
```

来看下效果:
![红-251,绿-252.png](http://upload-images.jianshu.io/upload_images/2229730-757190bc0601cd3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


此时,红色label抗拉伸值小于绿色,所有红色label被拉伸了

####  三.小结:
Content Hugging Priority 抗拉伸优先级
值越小,越容易被拉伸,
此属性在不手动计算UITableViewCell高度,UITableViewCell动态高度时,会起到重要作用,后面会结合实例来详细介绍.

----------------------
代码地址:<https://github.com/CoderZhuXH/AutoLayout>

-转载注明出处