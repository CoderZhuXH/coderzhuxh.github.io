---
layout: post
title: Swift自定义带角标TabBarController
categories: Swift
description: Swift开发
keywords: Swift, 自定义, TabBarController

---

在做即时通讯类APP中,经常需要在tabBar上显示数字角标几小红点,这时系统TabBarController已经不能满足需要了,需要自定义TabBarController,OC 自定义TabBarController相信大家都很熟悉,今天笔者来聊聊Swift中自定义TabBarController的实现,笔者顺便做下封装,方便调用.

###	需求:

1.	自定义TabBarController,传入`文字` `图片` `控制器` 名称数组,实现一行代码创建.
2.	能够自定义高度
3.	能够显示数字角标及小红点

###	效果

![image](https://raw.githubusercontent.com/CoderZhuXH/XHTabBar/master/DEMO.PNG)

###	实现

1.创建一个类(笔者命名XHTabBar)继承UITabBarController,定义变量,并定义其初始化方法如下:

```swift
class XHTabBar:UITabBarController {

    //记录选中button
    var seleBtn: UIButton?
    //tabbar高度
    var tabBarHeight:CGFloat = 49.0
    //title数组
    var titleArray = [String]()
    //默认图片数组
    var imageArray = [String]()
    //选中图片数组
    var selImageArray = [String]()
    //控制器数组
    var controllerArray = [String]()
    
/*
  自定义TabBarController初始化方法
- parameter controllerArray:控制器名称数组
- parameter titleArray:title数组
- parameter imageArray:默认图片数组
- parameter selImageArray:选中图片数组
- parameter height:TabBar高度
*/
init(controllerArray: [String], titleArray: [String],imageArray: [String],selImageArray: [String],height: CGFloat?) {

        self.controllerArray = controllerArray
        self.titleArray = titleArray
        self.imageArray = imageArray
        self.selImageArray = selImageArray
        if let tempHeight = height
        {
            tabBarHeight = tempHeight;
        }
        if tabBarHeight < 49.0
        {
            tabBarHeight = 49.0
        }
        super.init(nibName: nil, bundle: nil)
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

2.在viewDidLoad初始化自定义TabBar,代码如下

```swift
override func viewDidLoad() {
        super.viewDidLoad()
        //2.1添控制器
        addController()
        //2.2添加自定义TabBarView
        self.tabBar.addSubview(cusTabbar)
        //2.3添加TabBarButton
        addTabBarButton()
        //2.4处理高度大于49,TabBar顶部横线
        setupTabbarLine()
    }
```

2.1.添加控制器方法实现

```
    /**
     添加控制器
     */
    private func addController(){
        
        guard controllerArray.count > 0 else
        {
            print("error:控制器数组为nil")
            return
        }
        
        var navArray = [UIViewController]()
        //获取命名空间
        let ns = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
        
        for className in controllerArray {
            
            // 将类名转化为类
            let cls: AnyClass? = NSClassFromString(ns + "." + className)

            //Swift中如果想通过一个Class来创建一个对象, 必须告诉系统这个Class的确切类型
            guard let vcCls = cls as? UIViewController.Type else
            {
                print("error:cls不能当做UIViewController")
                return
            }
            let vc = vcCls.init()
            let nav = UINavigationController(rootViewController:vc)
            navArray.append(nav)
        }
        
        self.viewControllers  = navArray;
    }
``` 

2.2.添加自定义cusTabbar懒加载

```swift
    //MARK: - cusTabbar懒加载
    private lazy var cusTabbar: UIView = {
        
        let x = CGFloat(0)
        let y = 49.0 - self.tabBarHeight
        let width = MWIDTH
        let height = self.tabBarHeight
        
        let view = UIView(frame:CGRectMake(x,y,width,height))
        view.backgroundColor = ColorTabBar
        
        return view
    }()
```

2.3.添加tabBarButton方法实现

2.3.1.先自定义tabBarButton,使Button图片及文字呈上下布局

```swift
/*
   自定义tabBarButton
*/
class XHTabBarButton:UIButton {
    
    override init(frame: CGRect) {
        
        super.init(frame: frame)
        
        imageView?.contentMode = UIViewContentMode.ScaleAspectFit
        titleLabel?.textAlignment = NSTextAlignment.Center
        
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    //重定义image位置
    override func imageRectForContentRect(contentRect: CGRect) -> CGRect {
        let newX:CGFloat = 0.0
        let newY:CGFloat = 5.0
        let newWidth:CGFloat = CGFloat(contentRect.size.width)
        let newHeight:CGFloat = CGFloat(contentRect.size.height)*scale-newY
        return CGRectMake(newX, newY, newWidth, newHeight)
    }

    //重定义title位置
    override func titleRectForContentRect(contentRect: CGRect) -> CGRect {
        let newX: CGFloat = 0
        let newY: CGFloat = contentRect.size.height*scale
        let newWidth: CGFloat = contentRect.size.width
        let newHeight: CGFloat = contentRect.size.height-contentRect.size.height*scale
        return CGRectMake(newX, newY, newWidth, newHeight)
    }   
}
```

2.3.2.添加tabBarButton,及数字角标,小红点

```swift 
    /**
     添加tabBarButton
     */
    private func addTabBarButton()
    {
        let num = controllerArray.count
        for i in 0..<num {
            
            let  width = UIScreen.mainScreen().bounds.size.width
            let  x = CGFloat(width/CGFloat(num)*CGFloat(i))
            let  y:CGFloat = 0.0
            let  w = width/CGFloat(num)
            let  h = tabBarHeight
            let button = XHTabBarButton(frame:CGRectMake(x,y,w,h))
            
            button.tag = 1000+i
            button.setTitleColor(ColorTitle, forState: UIControlState.Normal)
            button.setTitleColor(ColorTitleSel, forState: UIControlState.Selected)
            button.titleLabel?.font = UIFont.systemFontOfSize(titleFontSize)
            button.setImage(UIImage.init(named:self.imageArray[i]), forState: UIControlState.Normal)
            button.setImage(UIImage.init(named: self.selImageArray[i]), forState: UIControlState.Selected)
            button.setTitle(self.titleArray[i], forState: UIControlState.Normal)
            button.addTarget(self, action:#selector(buttonAction(_:)), forControlEvents: UIControlEvents.TouchUpInside)
            
            cusTabbar.addSubview(button)
            
            //默认选中
            if i == 0 {
                
                button.selected = true
                self.seleBtn = button
            }
            
            //添加角标
            let numLabel = UILabel(frame: CGRectMake(button.frame.size.width/2.0+6, 3, numMarkD, numMarkD))
            numLabel.layer.masksToBounds = true
            numLabel.layer.cornerRadius = 10
            numLabel.backgroundColor = UIColor.redColor()
            numLabel.textColor = UIColor.whiteColor()
            numLabel.textAlignment = NSTextAlignment.Center
            numLabel.font = UIFont.systemFontOfSize(13)
            numLabel.tag = 1020+i
            numLabel.hidden = true
            button.addSubview(numLabel)
        }
    }
    //MARK: - TabBar Action
    @objc private func buttonAction(button: UIButton) {
        let index: Int = button.tag-1000
        self.showControllerIndex(index)
    }
```
3.定义TabBar角标,小红点,手动切换控制器等操作

3.1.设置数字角标

```swift
    /**
     *  设置数字角标
     *
     *  - param: num   所要显示数字
     *  - param: index 位置
     */
    func showBadgeMark(badge: Int, index: Int) {
        
        guard index < controllerArray.count else
        {
            print("error:index="+"\\(index)"+"超出范围")
            return;
        }
        
        let numLabel = (cusTabbar.viewWithTag(1020+index) as? UILabel)!
        numLabel.hidden = false
        var nFrame = numLabel.frame
        if badge <= 0 {
            //隐藏角标
            self.hideMarkIndex(index)
            
        } else {
            
            if badge > 0 && badge <= 9 {
                
                nFrame.size.width = numMarkD
                
            } else if badge > 9 && badge <= 19 {
                
                nFrame.size.width = numMarkD+5
                
            } else {
                
                nFrame.size.width = numMarkD+10
                
            }
            nFrame.size.height = numMarkD
            numLabel.frame = nFrame
            numLabel.layer.cornerRadius = numMarkD/2.0
            numLabel.text = "\\(badge)"
            if badge > 99 {
                numLabel.text = "99+"
            }
            
        }
    }
```

3.2.设置小红点

```swift
    /**
     *  设置小红点
     *
     *  - param: index 位置
     */
    func showPointMarkIndex(index: Int) {
        guard index < controllerArray.count else
        {
            print("error:index="+"\\(index)"+"超出范围")
            return;
        }
        let numLabel = (cusTabbar.viewWithTag(1020+index) as? UILabel)!
        numLabel.hidden = false
        var nFrame = numLabel.frame
        nFrame.size.height = pointMarkD
        nFrame.size.width = pointMarkD
        numLabel.frame = nFrame
        numLabel.layer.cornerRadius = pointMarkD/2.0
        numLabel.text = ""
    }
```

3.3.角标及小红点影藏

```swift
    /**
     *  影藏角标及小红点
     *
     *  - param: index 位置
     */
    func hideMarkIndex(index: Int) {
        guard index < controllerArray.count else
        {
            print("error:index="+"\\(index)"+"超出范围")
            return;
        }
        let numLabel = (cusTabbar.viewWithTag(1020+index) as? UILabel)!
        numLabel.hidden = true
    }

```

3.4.手动切换TabBar要显示的控制器

```swift
    /**
     *  手动切换要显示的控制器
     *
     *  - param: index 位置
     */
    func showControllerIndex(index: Int) {
        
        guard index < controllerArray.count else
        {
            print("error:index="+"\\(index)"+"超出范围")
            return;
        }
        self.seleBtn!.selected = false
        let button = (cusTabbar.viewWithTag(1000+index) as? UIButton)!
        button.selected = true
        self.seleBtn = button
        self.selectedIndex = index
    }
```

3.5.影藏TabBarController

```swift
        let controller = UIViewController.init()
        //隐藏TabBarController
        controller.hidesBottomBarWhenPushed = true
        self.navigationController?.pushViewController(controller, animated: true)
```

4.贴出上面用到的(类似OC宏定义)相关常量及方法,若需调整可直接修改下面属性的值

```swift
/**
 *  RGBA颜色
 */
func ColorRGBA(r:CGFloat,g:CGFloat,b:CGFloat,a:CGFloat) -> UIColor {
    
    return UIColor(red:r/255.0,green:g/255.0,blue:b/255.0,alpha:a)
}
/**
 *  RGB颜色
 */
func ColorRGB(r:CGFloat,g:CGFloat,b:CGFloat) -> UIColor {
    
    return ColorRGBA(r, g: g, b: b, a: 1.0)
}
/**
 *  随机色
 */
func ColorRandom() -> UIColor {
    
    return ColorRGB(CGFloat(arc4random()%255), g: CGFloat(arc4random()%255), b: CGFloat(arc4random()%255))
}
/**
 *  屏幕宽度
 */
let MWIDTH = UIScreen.mainScreen().bounds.size.width

/**
 *  屏幕高度
 */
let MHEIGHT = UIScreen.mainScreen().bounds.size.height

/**
 *  tabbar背景色
 */
private let ColorTabBar = UIColor.whiteColor()

/**
 *  title默认颜色
 */
private let ColorTitle = UIColor.grayColor()

/**
 *  title选中颜色
 */
private let ColorTitleSel = ColorRGB(41,g: 167,b: 245)

/**
 *  title字体大小
 */
private let titleFontSize : CGFloat = 12.0

/**
 *  数字角标直径
 */
private let numMarkD:CGFloat = 20.0

/**
 *  小红点直径
 */
private let pointMarkD:CGFloat = 12.0

/**
 *  button 图片与文字上下占比
 */
private let scale:CGFloat = 0.55

```

###	调用

```swift
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        
        window = UIWindow(frame:UIScreen.mainScreen().bounds)
        window?.backgroundColor = UIColor.whiteColor()
        
        /*
         控制器name数组
         */
        let  controllerArray = ["MainVC","MsgVC","FriendVC","MeVC"]
        /*
         title数组
         */
        let  titleArray = ["首页","消息","朋友","我的"]
        /*
         默认图片数组
         */
        let  imageArray = ["home_tabbar","msg_tabbar","friend_tabbar","me_tabbar"]
        /*
         选中图片数组
         */
        let  selImageArray = ["home_tabbar_sel","msg_tabbar_sel","friend_tabbar_sel","me_tabbar_sel"]
        /*
         tabbar高度, 传nil默认49
         */
        let height = CGFloat(49)
        
        /*
         创建tabBarController
         */
        let tabBarController = XHTabBar(controllerArray:controllerArray,titleArray: titleArray,imageArray: imageArray,selImageArray: selImageArray,height:height)
        
        /*
         设为根控制器
        */
         window?.rootViewController = tabBarController
        /*
         设置数字角标(可选)
         */
        tabBarController.showBadgeMark(100, index: 1)
                
        /*
         设置小红点(可选)
         */
        tabBarController.showPointMarkIndex(2)
        
        /*
         不显示小红点/数字角标(可选)
         */
        //tabBarController.hideMarkIndex(3)
        
        /*
         手动切换tabBarController 显示到指定控制器(可选)
         */
        //tabBarController.showControllerIndex(3)
      
        window?.makeKeyAndVisible()
        return true
    }
```

###	注意

1.	该项目通过 `动态获取命名空间` + `.` + `类名` 来创建类对象,如下:
```swift
let cls: AnyClass? = NSClassFromString(命名空间 + "." + 类名)
```
2.	实测中发现,当命名空间中含有 ` - ` 等特殊字符时 创建类对象会为 `nil`<br>
 
3.	项目命名空间默认为项目名称,当碰到类名称正确 创建类对象失败(即`报error:cls不能当做UIViewController错误时`)时,你可以:<br>
	1.修改项目名去掉 ` - `等特殊字符(不推荐,笔者推荐第二种方法)<br>
	2.到TARGETS -> Build Settings ->Produce Name 中修改命名空间,去掉命名空间中 ` - ` 等特殊字符:
	
![image](http://upload-images.jianshu.io/upload_images/2229730-d3bb56c6afae82d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###	小节

*    通过上面的封装可以实现传入数据源,一行代码初始化tabBarController,并且支持角标及小红点,很方便在今后项目中调用.
*    代码地址:<https://github.com/CoderZhuXH/XHTabBarSwift>