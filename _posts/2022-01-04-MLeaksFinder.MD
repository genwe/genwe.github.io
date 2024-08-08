---
layout: post
comments: true
title: MLeaksFinder源码阅读
---

去年十月份项目里接入了MLeaksFinder，期间也有不少声音反馈说影响开发及测试进度，一两个月后也有这样的声音，整个事件过程对我来说，套用网络流行语，“我看不懂，但我大受震撼”！

(MLeaksFinder不是我接入的，本地跑代码时我会将很多用不到的库注释掉)

上家公司的app相对来说属于小众类型，用户量小，使用时长短，几乎都在30分钟以内，而现行项目使用时长一般是大于2个小时的，对于app稳定性要求应该是很高的。

[MLeaksFinder](https://github.com/Tencent/MLeaksFinder/)是一个用来检测iOS内存泄漏的库，最后一次提交已经是4年前了，想较于前端的技术变化日新月异，这里好像时间静止了一样。

#### 下面一起看看文档

ARC中常见的内存泄漏是循环引用导致的Abandoned memory，Leaks工具查不出这类问题，Allocations工具虽然可以查出这类问题，但是使用繁琐

第三方开源库HeapInspector-for-iOS及MSLeakHunter有一些需要改进的地方

发现泄漏的提示弹框显示的View-ViewController stack从上往下看
```objc
Memory Leak
(
    MyTableViewController,
    UITableView,
    UITableViewWrapperView,
    MyTableViewCell
)
```
stack表示MyTableViewController,UITableView,UITableViewWrapperView已成功释放，而MyTableViewCell没有被释放

#### 原理
思路很简单，当一个控制器被pop或者dismiss之后，其相应的子view都会很快被释放；故在相应的控制器调用pop或者dismiss几秒钟(看代码现在设置的是2秒)之后查看其子view是否存在，存在则大概率出现了内存泄漏。

MLeaksFinder目前只检测ViewController及View对象是否存在内存泄漏，可以使用MLCheck宏手动扩展检测其他类型的对象

确定没问题的话，比如设计的缓存等情况下，可以重写willDealloc方法return NO来修正一些例外的情况

可以结合FBRetainCycleDetector一起使用来查找存在循环引用的对象，podfile中添加FBRetainCycleDetector依赖，打开MLeaksFinder.h中的MEMORY_LEAKS_FINDER_RETAIN_CYCLE_ENABLED宏就可以了。

#### 代码实现
查看源码实现，文件大致分为三类，

MLeakedObjectProxy(弱引用需要检测的类，使用Set存储需要检测的类，可用于循环引用的检查)

MLeaksMessenger(封装alertView方法)

NSObject+MemoryLeak分类及其他UI分类

如文档上所说，流程为控制器或者导航控制器的pop或者dismiss时调用-willDealloc方法，相应的UI分类实现自定义的willDealloc方法，为基类NSObject添加一个-willDealloc方法，UI分类中的方法会先调用基类的再做特殊处理，

基类中先检测相应的类是否包含在白名单里，是则跳过，否则继续执行，

判断是否是执行target-action操作，此时目标对象不检测内存泄漏，否认继续执行，

2秒后调用相应方法，若已释放则什么也不发生，否则判断是否与MLeakedObjectProxy中的集合有交集，有的话就不添加了，否则添加到MLeakedObjectProxy中，走检测循环引用的逻辑或者直接弹框

NSObject+MemoryLeak.m
```objc
- (BOOL)willDealloc {
    NSString *className = NSStringFromClass([self class]);
    if ([[NSObject classNamesWhitelist] containsObject:className])
        return NO;
    
    NSNumber *senderPtr = objc_getAssociatedObject([UIApplication sharedApplication], kLatestSenderKey);
    if ([senderPtr isEqualToNumber:@((uintptr_t)self)])
        return NO;
    
    __weak id weakSelf = self;
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        __strong id strongSelf = weakSelf;
        [strongSelf assertNotDealloc];
    });
    
    return YES;
}

- (void)assertNotDealloc {
    if ([MLeakedObjectProxy isAnyObjectLeakedAtPtrs:[self parentPtrs]]) {
        return;
    }
    [MLeakedObjectProxy addLeakedObject:self];
    
    NSString *className = NSStringFromClass([self class]);
    NSLog(@"Possibly Memory Leak.\nIn case that %@ should not be dealloced, override -willDealloc in %@ by returning NO.\nView-ViewController stack: %@", className, className, [self viewStack]);
}
```

#### TODO
另外一个思路[PLeakSniffer](https://github.com/music4kid/PLeakSniffer)，有时间看看

看一下[FBRetainCycleDetector](https://github.com/facebook/FBRetainCycleDetector)是怎么实现找到一个block的所有强引用对象的？


#### 一些思考

MLeaksFinder整体看下来感觉不是很晦涩难懂，且实践使用时效果明显，知识点好像大家都懂，为什么你没有写出一个有用的库出来呢？

设置是否启动及是否检测循环引用等配置需要修改源码，通过宏控制，而不是通过外部设置参数，好像也只能这样，不然就要强依赖其他第三方了

MLeaksFinder如果不手设置，默认只检测UI部分的内存泄漏，是不是什么时候专项搞一下基础库可能存在的内存泄漏问题呢。某App八月份时的内存截图，彩蛋自取！

![_config.yml](/images/MLeaksFinder.png)

万丈高楼平地起，勿在浮沙筑高台。

与君共勉！

***
### 引用源
+ [MLeaksFinder：精准 iOS 内存泄露检测工具](http://wereadteam.github.io/2016/02/22/MLeaksFinder/)
+ [MLeaksFinder 新特性](http://wereadteam.github.io/2016/07/20/MLeaksFinder2/)
 
