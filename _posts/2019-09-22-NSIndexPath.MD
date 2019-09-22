---
layout: post
comments: true
title: NSIndexPath知识扩展
---

如果您用过TableView, 那一定知道NSIndexPath,这里带大家稍微扩展一下对NSIndexPath的认识。

## NSIndexPath简介
直译这个单词，为索引路径。

A list of indexes that together represent the path to a specific location in a tree of nested arrays.

用来表示嵌套数组树中特定位置的路径的索引列表。索引路径中的每个索引代表从树中一个节点到另一个更深节点的节点数组的索引。


Index path 1.4.3.2的图片表示如下：

![_config.yml](https://docs-assets.developer.apple.com/published/1dd4b9d8e4/indexpath_df7d08bf-e846-4963-bc2c-d81442fce0e8.gif)

常用方法：
```objc
NSUInteger indexs4[] = {1, 2, 3 , 4};
        
NSIndexPath *indexPath1 = [NSIndexPath indexPathWithIndexes:(const NSUInteger * _Nullable)indexs4 length:4];

NSIndexPath *indexPath2 = [NSIndexPath indexPathWithIndexes:(const NSUInteger * _Nullable)indexs4 length:4];

NSIndexPath *indexPath3 = [indexPath1 indexPathByAddingIndex:5];

NSIndexPath *indexPath4 = [indexPath3 indexPathByRemovingLastIndex];

NSUInteger length3 = indexPath3.length;

NSUInteger length4 = indexPath4.length;

NSUInteger indexAtPosition = [indexPath4 indexAtPosition: length4 - 1];

NSUInteger indexs[3];

[indexPath4 getIndexes:indexs range:NSMakeRange(1, 3)];
                                      
NSLog(@"\n%@\n%@\n%@\n%@\n%lu\n%lu\n%lu", indexPath1, indexPath2, indexPath3, indexPath4, (unsigned long)length3, (unsigned long)length4, (unsigned long)indexAtPosition);
```

输出如下：
```objc
<NSIndexPath: 0xbfb979580fc79d65> {length = 4, path = 1 - 2 - 3 - 4}
<NSIndexPath: 0xbfb979580fc79d65> {length = 4, path = 1 - 2 - 3 - 4}
<NSIndexPath: 0x100607ab0> {length = 5, path = 1 - 2 - 3 - 4 - 5}
<NSIndexPath: 0xbfb979580fc79d65> {length = 4, path = 1 - 2 - 3 - 4}
5
4
4
```

NSIndexPath 是属于 Foundation框架的，而我们常用的方法是 属于 UIKit 对其的扩展(NSIndexPath+UIKitAdditions.h), 为了便于与UITableView和UICollectionView一起使用，此类别提供了便利方法indexPathForRow:inSection:、indexPathForItem:inSection: 和 属性(如：section、row、item)，可以更轻松地使用NSIndexPath来表示节、行、项的概念。

## 比较的方法
	// 比较方法的支持
  ```objc
  - (NSComparisonResult)compare:(NSIndexPath *)otherObject;
  ```
官方提供的比较方法，当调用排序方法时，默认使用，得到一个以深度优先遍历顺序顺序表示节点的数组；

对比：
```objc
NSUInteger indexs1[] = {1, 2, 3 , 4};
NSUInteger indexs2[] = {1, 2, 3 , 4};
NSUInteger indexs3[] = {1, 2, 3 , 4, 5};

NSIndexPath *indexPath1 = [NSIndexPath indexPathWithIndexes:(const NSUInteger * _Nullable)indexs1 length:4];
NSIndexPath *indexPath2 = [NSIndexPath indexPathWithIndexes:(const NSUInteger * _Nullable)indexs2 length:4];
NSIndexPath *indexPath3 = [NSIndexPath indexPathWithIndexes:(const NSUInteger * _Nullable)indexs3 length:5];

NSComparisonResult result1 = [indexPath1 compare:indexPath2];
BOOL result2 = indexPath1 == indexPath2;
BOOL result3 = [indexPath1 isEqual:indexPath2];

NSComparisonResult result4 = [indexPath1 compare:indexPath3];
BOOL result5 = indexPath1 == indexPath3;
BOOL result6 = [indexPath1 isEqual:indexPath3];

NSLog(@"\n%@\n%@\ncompare:%lu\n==:%d\nisEqual:%d", indexPath1, indexPath2, result1, result2, result3);

NSLog(@"\n%@\n%@\ncompare:%lu\n==:%d\nisEqual:%d", indexPath1, indexPath3, result4, result5, result6);
```
输出：
```objc
<NSIndexPath: 0xb485641070940d95> {length = 4, path = 1 - 2 - 3 - 4}
<NSIndexPath: 0xb485641070940d95> {length = 4, path = 1 - 2 - 3 - 4}
compare:0
==:1
isEqual:1

<NSIndexPath: 0xb485641070940d95> {length = 4, path = 1 - 2 - 3 - 4}
<NSIndexPath: 0x102864480> {length = 5, path = 1 - 2 - 3 - 4 - 5}
compare:18446744073709551615
==:0
isEqual:0
Printing description of result4:
(NSComparisonResult) result4 = NSOrderedAscending
```

一般来说，对基础数据类型使用==运算符对比的是值，对对象使用比较的是对象的地址；

从上面的例子中可以看出，使用相同的初始数据使用类方法初始化得到的对象地址是一样的，使用indexPathForRow:inSection方法效果也是一样的；如果不重写hash方法情况下，hash值也是一样的；

使用对象方法，每次初始化都是不同的对象；

## 结论：
在TableView中使用类方法创建的NSIndexPath做比较时，直接==是没问题的，不用分别对比section和row。




## 引用源
+ [NSIndexPath的苹果官网文档](https://developer.apple.com/documentation/foundation/nsindexpath?language=objc)