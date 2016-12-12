---
layout: post
title: iOS开屏广告解决方案XHLaunchAd3.0
categories: iOS
description: iOS开发
keywords: iOS, 开屏广告, XHLaunchAd

---

之前由于项目需要,写了一个开屏广告组件XHLaunchAd,主要自用,后来发现越来越多的开发者,有这个需求.<br>
随着使用人数的增加,部分开发者使用者提出了一些需求和问题,XHLaunchAd并不能很好的解决.<br>
于是决定最近抽时间,把这个组件优化重构一下,解决网友之前提出一些问题和需求,并增加一些新功能,发布v3.0.0版本.<br>
该项目目前已经收到 700 多个 star ,目前已经更新到v3.0.1版本<br>


```objc
-> XHLaunchAd (3.0.1)
   开屏广告解决方案,支持图片/视频、静态/动态、全屏/半屏广告,支持iPhone/iPad,自带图片下载、缓存功能,无其他三方依赖
   pod 'XHLaunchAd', '~> 3.0.1'
   - Homepage: https://github.com/CoderZhuXH/XHLaunchAd
   - Source:   https://github.com/CoderZhuXH/XHLaunchAd.git
   - Versions: 3.0.1, 3.0.0, 2.2.2, 2.2.1, 2.2.0, 2.1.9, 2.1.8, 2.1.7, 2.1.6,
   2.1.5, 2.1.4, 2.1.3, 2.1.2, 2.1.1, 2.1.0, 2.0.1, 2.0, 1.2, 1.1.4, 1.1.3,
   1.1.2, 1.1, 1.0 [master repo]
```

###	主要更新:

1.增加mp4视频开屏广告<br>
2.增加对本地资源支持<br>
3.增加预缓存接口<br>
4.增加更多属性及接口,具有更强的自定义性<br>
5.可设置显示完成动画类型<br>
6.可自定义跳过按钮<br>
7.拥有更优雅的接入接口<br>
8.优化缓存机制,bug fix等等<br>

###	接入后效果:

![ScreenShot00.gif](http://upload-images.jianshu.io/upload_images/2229730-866e8214ba1904fa.gif?imageMogr2/auto-orient/strip)

![ScreenShot06.gif](http://upload-images.jianshu.io/upload_images/2229730-0347e21e70b9bb87.gif?imageMogr2/auto-orient/strip)

![ScreenShot05.gif](http://upload-images.jianshu.io/upload_images/2229730-d4f02650f4bbb64f.gif?imageMogr2/auto-orient/strip)

![ScreenShot02.gif](http://upload-images.jianshu.io/upload_images/2229730-5cd1dab1a302b122.gif?imageMogr2/auto-orient/strip)

![ScreenShot01.gif](http://upload-images.jianshu.io/upload_images/2229730-b6a99706be7793e4.gif?imageMogr2/auto-orient/strip)

###	使用方法:

在AppDelegate didFinishLaunchingWithOptions方法中添加下面代码(提前设置App启动页为LaunchImage)

1.添加图片开屏广告⬇️

1.1使用默认配置快速初始化

```objc
//1.使用默认配置初始化
    XHLaunchImageAdConfiguratuon *imageAdconfiguratuon = [XHLaunchImageAdConfiguratuon defaultConfiguratuon];
    //广告图片URLString/或本地图片名(.jpg/.gif请带上后缀)
    imageAdconfiguratuon.imageNameOrURLString = @"image0.jpg";
    //广告点击打开链接
    imageAdconfiguratuon.openURLString = @"http://www.returnoc.com";
    //显示图片开屏广告
    [XHLaunchAd imageAdWithImageAdConfiguration:imageAdconfiguratuon delegate:self];
```
1.2自定义配置初始化

```objc
//2.自定义配置初始化
    XHLaunchImageAdConfiguratuon *imageAdconfiguratuon = [XHLaunchImageAdConfiguratuon new];
    //广告停留时间
    imageAdconfiguratuon.duration = 5;
    //广告frame
    imageAdconfiguratuon.frame = CGRectMake(0, 0, [UIScreen mainScreen].bounds.size.width, [UIScreen mainScreen].bounds.size.height-150);
    //广告图片URLString/或本地图片名(.jpg/.gif请带上后缀)
    imageAdconfiguratuon.imageNameOrURLString = @"image0.jpg";
    //网络图片缓存机制(只对网络图片有效)
    imageAdconfiguratuon.imageOption = XHLaunchAdImageRefreshCached;
    //图片填充模式
    imageAdconfiguratuon.contentMode = UIViewContentModeScaleToFill;
    //广告点击打开链接
    imageAdconfiguratuon.openURLString = @"http://www.returnoc.com";
    //广告显示完成动画
    imageAdconfiguratuon.showFinishAnimate =ShowFinishAnimateFadein;
    //跳过按钮类型
    imageAdconfiguratuon.skipButtonType = SkipTypeTimeText;
    //后台返回时,是否显示广告
    imageAdconfiguratuon.showEnterForeground = NO;
    //显示图片开屏广告
    [XHLaunchAd imageAdWithImageAdConfiguration:imageAdconfiguratuon delegate:self]; 
    
```
2.添加图片开屏广告⬇️

2.1 使用默认配置快速初始化

```objc
//1.使用默认配置初始化
    XHLaunchVideoAdConfiguratuon *videoAdconfiguratuon = [XHLaunchVideoAdConfiguratuon defaultConfiguratuon];
    //广告视频URLString/或本地视频名(请带上后缀)
    videoAdconfiguratuon.videoNameOrURLString = @"video0.mp4";
    //广告点击打开链接
    videoAdconfiguratuon.openURLString = @"http://www.returnoc.com";
    //显示视频开屏广告
    [XHLaunchAd videoAdWithVideoAdConfiguration:videoAdconfiguratuon delegate:self];
```
2.2 自定义配置初始化

```objc   
//2.自定义配置
    XHLaunchVideoAdConfiguratuon *videoAdconfiguratuon = [XHLaunchVideoAdConfiguratuon new];
    //广告停留时间
    videoAdconfiguratuon.duration = 5;
    //广告frame
    videoAdconfiguratuon.frame = CGRectMake(0, 0, [UIScreen mainScreen].bounds.size.width, [UIScreen mainScreen].bounds.size.height);
    //广告视频URLString/或本地视频名(请带上后缀)
    videoAdconfiguratuon.videoNameOrURLString = @"video1.mp4";
    //视频填充模式
    videoAdconfiguratuon.scalingMode = MPMovieScalingModeAspectFill;
    //广告点击打开链接
    videoAdconfiguratuon.openURLString =  @"http://www.returnoc.com";
    //广告显示完成动画
    videoAdconfiguratuon.showFinishAnimate =ShowFinishAnimateFadein;
     //跳过按钮类型
    videoAdconfiguratuon.skipButtonType = SkipTypeTimeText;
    //后台返回时,是否显示广告
    videoAdconfiguratuon.showEnterForeground = NO;
    //显示视频开屏广告
    [XHLaunchAd videoAdWithVideoAdConfiguration:videoAdconfiguratuon delegate:self];
```
###	注意:

若你的广告图片/视频URL来源于数据请求,请在请求数据前设置等待时间,在数据请求成功回调里,配置广告,如下:

```objc
//1.因为数据请求是异步的,请在数据请求前,调用下面方法配置数据等待时间.
//2.设为3即表示,启动页将停留3s等待服务器返回广告数据,3s内等到广告数据,将正常显示广告,否则将自动进入window的RootVC

  //设置数据等待时间
    [XHLaunchAd setWaitDataDuration:3];
    //广告数据请求
    [Network getLaunchAdImageDataSuccess:^(NSDictionary * response) {
        
      //在此处利用服务器返回的广告数据,按上面示例添加开屏广告代码
      XHLaunchImageAdConfiguratuon *imageAdconfiguratuon = [XHLaunchImageAdConfiguratuon ... 
     //配置相关参数.... 
     //显示开屏广告
     [XHLaunchAd imageAdWithImageAdConfiguration:imageAdconfiguratuon delegate:self];
              
    } failure:^(NSError *error) {
    }];   
    
```
3.点击事件

```objc
/**
 *  广告点击事件 回调
 */
- (void)xhLaunchAd:(XHLaunchAd *)launchAd clickAndOpenURLString:(NSString *)openURLString;
{
    if(openURLString)
    {
    	 //跳转到广告详情页面
        WebViewController *VC = [[WebViewController alloc] init];
        VC.URLString = openURLString;
        [self.window.rootViewController presentViewController:VC animated:YES completion:nil];
        
    }
}

```

4.自定义跳过按钮

```objc
//1.XHLaunchImageAdConfiguratuon 和XHLaunchVideoAdConfiguratuon 均有一个configuratuon.customSkipView 属性
//2.自定义一个skipView 赋值给configuratuon.customSkipView属性 便可替换默认跳过按钮 如下:
configuratuon.customSkipView = [self customSkipView];

-(UIView *)customSkipView
{
    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    button.backgroundColor =[ UIColor clearColor];
    button.layer.cornerRadius = 3.0;
    button.layer.borderWidth = 1.0;
    button.layer.borderColor = [UIColor whiteColor].CGColor;
    [button setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
    button.titleLabel.font = [UIFont systemFontOfSize:13];
    button.frame = CGRectMake(15,[UIScreen mainScreen].bounds.size.height-55, 85, 40);
    [button addTarget:self action:@selector(skipAction) forControlEvents:UIControlEventTouchUpInside];
    return button;
}

-(void)skipAction
{
    [XHLaunchAd skipAction];
}

/**
 *  代理方法-倒计时回调
 *
 *  @param launchAd XHLaunchAd
 *  @param duration 倒计时时间
 */
-(void)xhLaunchAd:(XHLaunchAd *)launchAd customSkipView:(UIView *)customSkipView duration:(NSInteger)duration
{
    UIButton *button = (UIButton *)customSkipView;//此处转换为你之前的类型
    //设置自定义跳过按钮倒计时
    [button setTitle:[NSString stringWithFormat:@"自定义%lds",duration] forState:UIControlStateNormal];
}
```

5.预缓存接口(如果你需要提前下载并缓存广告图片或视频 请调用下面方法)

```objc
/**
 *  批量下载并缓存image(异步)
 *
 *  @param urlArray image URL Array
 */
+(void)downLoadImageAndCacheWithURLArray:(NSArray <NSURL *> * )urlArray;

/**
 *  批量下载并缓存视频(异步)
 *
 *  @param urlArray 视频URL Array
 */
+(void)downLoadVideoAndCacheWithURLArray:(NSArray <NSURL *> * )urlArray;
```

6.其他代理方法

```objc
/**
 *  图片下载完成/或本地图片读取完成 回调
 *
 *  @param launchAd XHLaunchAd
 *  @param image    image
 */
-(void)xhLaunchAd:(XHLaunchAd *)launchAd imageDownLoadFinish:(UIImage *)image
{
    NSLog(@"图片下载完成/或本地图片读取完成回调");
}
/**
 *  视频下载完成回调
 *
 *  @param launchAd XHLaunchAd
 *  @param pathURL  视频保存在本地的path
 */
-(void)xhLaunchAd:(XHLaunchAd *)launchAd videoDownLoadFinish:(NSURL *)pathURL
{
    NSLog(@"video下载/加载完成/保存path = %@",pathURL.absoluteString);
}

/**
 *  视频下载进度回调
 */
-(void)xhLaunchAd:(XHLaunchAd *)launchAd videoDownLoadProgress:(float)progress total:(unsigned long long)total current:(unsigned long long)current
{
    NSLog(@"总大小=%lld,已下载大小=%lld,下载进度=%f",total,current,progress);
    
}
/**
 *  广告显示完成
 */
-(void)xhLaunchShowFinish:(XHLaunchAd *)launchAd
{
    NSLog(@"广告显示完成");
}

/**
 如果你想用SDWebImage等框架加载网络广告图片,请实现此代理
 
 @param launchAd          XHLaunchAd
 @param launchAdImageView launchAdImageView
 @param url               图片url
 */
-(void)xhLaunchAd:(XHLaunchAd *)launchAd launchAdImageView:(UIImageView *)launchAdImageView URL:(NSURL *)url
{
    [launchAdImageView sd_setImageWithURL:url];

}

```

7.其他操作

```objc

/**
 *  清除XHLaunch本地缓存
 */
+(void)clearDiskCache;

/**
 *  获取XHLaunch本地缓存大小(M)
 */
+(float)diskCacheSize;

/**
 *  缓存路径
 */
+(NSString *)xhLaunchAdCachePath;

```
###	小结:

[XHLaunchAd 3.0](https://github.com/CoderZhuXH/XHLaunchAd) 拥有更灵活的调用方式和更优雅的接口,后期我还会持续维护并更新他,希望能给更多开发者带来便利!<br>
代码地址:<https://github.com/CoderZhuXH/XHLaunchAd>