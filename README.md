# dennisxu-ios-tips
##收集一些iOS开发常用的小技巧
* [plist中获取数据赋给字典](#mark1)
* [UIColorFromRGB(rgbValue) 工具宏](#mark2)
* [正确的画1px线](#mark3)
* [工具宏-设备相关-屏幕、系统](#mark4)
* [NSInteger转NSString 工具宏](#mark5)
* [工具宏-图片相关](#mark6)
* [工具宏-颜色相关](#mark7)
* [获得当前屏幕截屏](#mark8)
* [添加拷贝剪切板](#mark9)
* [xcode7 app 不能联网解决办法](#mark10)

---
<a name="mark1"></a>
* 从plist中获取数据赋给字典

```Objective-C
NSString *plistPath = [[NSBundle mainBundle] pathForResource:@"book" ofType:@"plist"];
NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:plistPath];
```
<a name="mark2"></a>
* UIColorFromRGB(rgbValue)工具宏

```Objective-C
#define UIColorFromRGB(rgbValue) ([UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0])
```
<a name="mark3"></a>
* 正确的画1px线

```Objective-C
-(UIView*)generateSimpleLineView:(CGRect)frame{
    CGFloat single_line_width = (1 / [UIScreen mainScreen].scale);
    CGFloat sinle_line_adjust_offset = ((1 / [UIScreen mainScreen].scale) / 2);
    CGRect resultFrame = CGRectZero;
    if(frame.size.width>frame.size.height){
        //横线
        CGFloat offset = 0;
        if((int)frame.origin.y % 2 !=0){
            //需要调整
            offset = sinle_line_adjust_offset;
        }
        resultFrame = CGRectMake(frame.origin.x, frame.origin.y-offset, frame.size.width, single_line_width);
    }else{
        CGFloat offset = 0;
        if((int)frame.origin.x % 2 !=0){
            //需要调整
            offset = sinle_line_adjust_offset;
        }
        resultFrame = CGRectMake(frame.origin.x-offset, frame.origin.y, single_line_width, frame.size.height);
    }
    UIView *resultView = [[UIView alloc] initWithFrame:resultFrame];
    return resultView;
}

```
<a name="mark4"></a>
* 设备相关-屏幕、系统、等等 工具宏

```Objective-C
//获取屏幕尺寸
#define SCREEN_WIDTH ([UIScreen mainScreen].bounds.size.width)
#define SCREEN_HEIGHT ([UIScreen mainScreen].bounds.size.height)

//获取应用尺寸
#define APP_WIDTH [[UIScreen mainScreen]applicationFrame].size.width
#define APP_HEIGHT [[UIScreen mainScreen]applicationFrame].size.height

//当前系统版本号
#define IOS_VERSION                         ([[UIDevice currentDevice].systemVersion floatValue])
//是否在某版本之后
#define isCurrentDeviceSystemVersionLater(__Version__)      (IOS_VERSION >= (__Version__))

//判断是否 Retina屏、设备是否%fhone 5、是否是iPad
#define isRetina ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 960), [[UIScreen mainScreen] currentMode].size) : NO)
#define iPhone5 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 1136), [[UIScreen mainScreen] currentMode].size) : NO)
#define isPad (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)

//判断是真机还是模拟器
#if TARGET_OS_IPHONE
//iPhone Device
#endif
#if TARGET_IPHONE_SIMULATOR
//iPhone Simulator
#endif

//是否是iPhone5
#define iPhone5 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 1136), [[UIScreen mainScreen] currentMode].size) : NO)
//是否是iPhone4
#define iPhone4 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(640, 960), [[UIScreen mainScreen] currentMode].size) : NO)
```
<a name="mark5"></a>
* NSInteger转NSString 工具宏

```Objective-C
//NSInteger转字符串
#define itos(l) [NSString stringWithFormat:@"%ld", (long)(l)]
```
<a name="mark6"></a>
* 工具宏-图片相关

```Objective-C
//读取本地图片
#define LOADIMAGE(file,ext) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle]pathForResource:file ofType:ext]]

//定义UIImage对象
#define IMAGE(A) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle] pathForResource:A ofType:nil]]

//定义UIImage对象
#define ImageNamed(_pointer) [UIImage imageNamed:_pointer]

//可拉伸的图片
#define ResizableImage(name,top,left,bottom,right) [[UIImage imageNamed:name] resizableImageWithCapInsets:UIEdgeInsetsMake(top,left,bottom,right)]
#define ResizableImageWithMode(name,top,left,bottom,right,mode) [[UIImage imageNamed:name] resizableImageWithCapInsets:UIEdgeInsetsMake(top,left,bottom,right) resizingMode:mode]
//建议使用前两种宏定义,性能高于后者

```
<a name="mark7"></a>
* 工具宏-颜色相关
 
```Objective-C
// rgb颜色转换（16进制->10进制）
#define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

// 获取RGB颜色
#define RGBA(r,g,b,a) [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:a]
#define RGB(r,g,b) RGBA(r,g,b,1.0f)

//背景色
#define BACKGROUND_COLOR [UIColor colorWithRed:242.0/255.0 green:236.0/255.0 blue:231.0/255.0 alpha:1.0]

//清除背景色
#define CLEARCOLOR [UIColor clearColor]
```
<a name="mark8"></a>
* 获得当前屏幕截屏

```Objective-C
//获得当前屏幕截图
+(UIImage*)getCurrentSnapshot{
    UIWindow *window = [[UIApplication sharedApplication].delegate window];
    UIGraphicsBeginImageContext(window.rootViewController.view.bounds.size);
    [window.rootViewController.view.layer renderInContext:UIGraphicsGetCurrentContext()];
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    //可以存入相册
    //    UIImageWriteToSavedPhotosAlbum(image, self, nil, nil);
    UIGraphicsEndImageContext();
    return image;
}
```

<a name="mark9"></a>
* 添加拷贝剪切板

```Objective-C
UIPasteboard *pasteboard = [UIPasteboard generalPasteboard];
    pasteboard.string =
```

<a name="mark10"></a>
* xcode7 app 不能联网解决办法

新装的Xcode7 编译程序 出现 #warning: 获取app配置信息失败: The resource could not be loaded because the App Transport Security 我开始以为是xcode没优化好的缘故，其实这是苹果加大安全的管控，将以往HTTP协议强制改为HTTPS协议，以后的APP应该都是了，根本解决办法 改协议，临时解决办法在Info.plist中添加  NSAppTransportSecurity 类型  Dictionary Dictionary 下添加  NSAllowsArbitraryLoads 类型 Boolean ,值设为  YES，搞定,XCODE7