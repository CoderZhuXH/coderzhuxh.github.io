---
layout: post
title: iOS中atomic和nonatomic区别及内部实现
categories: Objective-C
description: iOS开发
keywords: iOS,atomic,nonatomic

---

iOS开发中使用@property声明属性时,经常用到atomic与nonatomic两个关键字....

![](http://upload-images.jianshu.io/upload_images/2229730-b931e153e0c8c8e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  前言
iOS开发中使用@property声明属性时，经常用到atomic与nonatomic两个关键字
```objc
@property(nonatomic,strong)UIImage *icon;
@property(strong)UIImage *icon1;//不写默认atomic
```
atomic与nonatomicd的主要区别就是系统自动生成的getter/setter方法不一样

*  atomic系统自动生成的getter/setter方法会进行加锁操作
*  nonatomic系统自动生成的getter/setter方法不会进行加锁操作

### atomic

系统生成的getter/setter方法会进行加锁操作,注意:这个锁仅仅保证了getter和setter存取方法的线程安全.

因为getter/setter方法有加锁的缘故,故在别的线程来读写这个属性之前,会先执行完当前操作.

例如:
线程1调用了某一属性的setter方法并进行到了一半,线程2调用其getter方法,那么会执行完setter操作后,在执行getter操作,线程2会获取到线程1 setter后的完整的值.

当几个线程同时调用同一属性的setter、getter方法时,会get到一个完整的值,但get到的值不可控.

例如:
线程1 调用getter
线程2 调用setter
线程3 调用setter
这3个线程并行同时开始,线程1会get到一个值,但是这个值不可控,可能是线程2,线程3 set之前的原始值,可能是线程2 set的值,也可能是线程3 set的值

####  atomic是线程安全的吗?

不是,
很多文章谈到atomic和nonatomic的区别时,都说atomic是线程安全,其实这个说法是不准确的.
atomic只是对属性的getter/setter方法进行了加锁操作,这种安全仅仅是set/get 的读写安全,并非真正意义上的线程安全,因为线程安全还有读写之外的其他操作(比如:如果当一个线程正在get或set时,又有另一个线程同时在进行release操作,可能会直接crash)


###  nonatomic

系统生成的getter/setter方法没有加锁
线程不安全,但更快
当多个线程同时访问同一个属性,会出现无法预料的结果

###  内部实现
下面我们新建一个nonatomic和一个atomic变量,并用代码演示其内部实现

```objc
//interface
@property(nonatomic,strong)UIImage *icon;//nonatomic
@property(strong)UIImage *icon1;//atomic

```

nonatomic对象setter和getter方法的实现

```objc

//mrc 环境
//implementation
@synthesize icon = _icon;

//set
-(void)setIcon:(UIImage *)icon
{
    if(_icon != icon)
    {
        [_icon release];
        _icon = [icon retain];
    }
}
//get
-(UIImage *)icon
{
    return _icon;
}

```

atomic对象setter和getter方法的实现

```objc
//mrc 环境
//implementation
@synthesize icon1 = _icon1;

//set
-(void)setIcon1:(UIImage *)icon1
{
    //同步代码块
    @synchronized (self) {
        
        if(_icon1 != icon1)
        {
            [_icon1 release];
            _icon1 = [icon1 retain];
        }
    }
}
//get
-(UIImage *)icon1
{
    UIImage *image = nil;
    //同步代码块
    @synchronized (self) {
        
        image = [[_icon1 retain] autorelease];
    }
    return image;
}

```
###  总结
*  atomic只是保证了getter和setter存取方法的线程安全,并不能保证整个对象是线程安全的,因此在多线程编程时,线程安全还需要开发者自己来处理.

*  关于选择:atomic系统生成的getter、setter会保证get、set操作的安全性,但相对nonatomic来说,atomic要更耗费资源,且速度要慢,故在iPhone等小型设备上,如果没有多线程之间的通讯,使用nonatomic是更好的选择

代码地址:<https://github.com/CoderZhuXH/MyBlogExample>

-转载注明出处