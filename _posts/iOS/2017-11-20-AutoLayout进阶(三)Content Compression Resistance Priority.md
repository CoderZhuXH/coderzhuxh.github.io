---
layout: post
title: AutoLayout进阶(三)Content Compression Resistance Priority
categories: Objective-C
description: iOS开发
keywords: iOS, AutoLayout , Content Compression Resistance Priority

---


本文将给大家介绍AutoLayout的Content Compression Resistance,说通俗一点就是"视图的抗压缩优先级".

![](http://upload-images.jianshu.io/upload_images/2229730-01223ec7dd9190c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  一.  本篇重点介绍:Content Compression Resistance Priority

许多开发者可能对这个属性比较陌生,同样这个属性对有intrinsic content size的控件（例如button，label）非常重要.
注:具有intrinsic content size的控件当你设置内容后,它可以根据内容多少来调整自己大小

#####  1.  Content Compression Resistance Priority:

视图抗压缩优先级,
值越小,视图越容易被压缩
此值默认为750

#####  2.  使用场景:

当一个视图上有多个intrinsic content size的子视图动态尺寸,并且子视图可能超出父视图区域时,此属性可控制哪些视图被内容被优先压缩.使其不超出父视图区域.

#####  3.  Content Compression Resistance Priority在约束界面如下位置:

![Content Compression Resistance.png](http://upload-images.jianshu.io/upload_images/2229730-510acec4d439e178.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####  二.  代码示例:

1.新建工程,我们添加一个固定大小的视图view(此view已添加约束,大小固定),在这个view上放2个label,分别为红绿,分别对其添加如下约束:

```objc
红label约束:上0,左0,下0
绿label约束:上0,左0,下0,右0
```
均不给宽度约束,宽度由label文字多少自己决定,
此时你会发现,添加完以上约束后,会报下面错误:
![demo.png](http://upload-images.jianshu.io/upload_images/2229730-6fb1e589a5576b26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这个错误的意思是说:

*  这2个label都是动态宽度,宽度由文字多少决定(未添加宽度约束),
*  当这2个label实际宽度之和小于父view宽度时,AutoLayout不知道优先拉伸哪个label
*   当这2个label实际宽度之和大于父view宽度时,AutoLayout不知道优先压缩哪个label
*    让我们添加Content Hugging Priority抗拉伸和Content Compression Resistance抗压缩优先级.

下面我们设置这两个label文字都为"很长很很长很长很长的文字",使2个label宽度和超过父view宽度,
依次修改这2个label 横向Content Compression Resistance Priority值分别为:

```objc
红label: Horizontal: 750
绿label:  Horizontal: 749
```

添加完成后错误消失,效果如下:
![红色-750,绿色-749.png](http://upload-images.jianshu.io/upload_images/2229730-09653b6cc4728913.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



因为绿色label抗压缩优先级为749,(优先级越小越容易被压缩),小于红色label,所有此时可以看到,当二者总宽度超出父view宽度时,绿色label优先被压缩了


下面我们来调换2个label横向压缩优先级如下:

```objc
红label: Horizontal: 749
绿label:  Horizontal: 750
```
再来看下效果:

![红色-749,绿色-750.png](http://upload-images.jianshu.io/upload_images/2229730-acaaf020bb30c25b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时,红色label被压缩了,红色abel抗拉伸优先级小于绿色label(越小越先被压缩),此时AutoLayout压缩红色label内容

####  三.小结:

Content Compression Resistance 抗压缩优先级
优先级越小,越先被压缩,
此属性在多个子视图动态尺寸,并子视图内容可能超出父视图区域时,会起到重要作用,后面会结合实例来详细介绍.

----------------------
代码地址:https://github.com/CoderZhuXH/AutoLayout

-转载注明出处