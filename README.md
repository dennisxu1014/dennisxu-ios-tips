# dennisxu-ios-tips
##收集一些iOS开发常用的小技巧

* 从plist中获取数据赋给字典
```Objective-C
NSString *plistPath = [[NSBundle mainBundle] pathForResource:@"book" ofType:@"plist"];
NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:plistPath];
```

