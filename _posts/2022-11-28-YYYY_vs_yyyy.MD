---
layout: post
comments: true
title: 日期格式化YYYY与yyyy区别
---

日期格式化YYYY与yyyy区别

#### 缘起
以前好像年底左右看到过关于YYYY文件的文章，但是没往心里去，或者过去很久，没多大印象了；

偶尔看到别人改代码时，有将YYYY盖成yyyy，提代码的备注只是写了避免潜在风险，具体什么风险并没有提；

搜了一下相关的资料，很多说的不清楚，😮‍💨，搞不清楚到底什么时候有问题，这里记录一下吧；

#### YYYY含义
YYYY代表Week Year,表示当前周所在的年份；Week Year下，每周仅属于某一年；

Week Year 存在两种标准：
ISO 8601：国际标准，每周从周一开始，每年的第一周至少包含 4 天

~~Common：通用标准，每周从周日开始，每年的第一周至少包含 1 天~~

关注国际标准就好了

使用YYYY格式输出时，存在问题是可能在年末或年初时，当天常规意识上明明属于当年，但是获取时，却显示前一年或后一年；
![_config.yml](/images/2015_12.png)

比如2015.12.28~31有四天，所以这一周属于2015年，而2016.01.01~03因为也属于2015年，则使用YYYY显示时会出现错误；


![_config.yml](/images/2019_12.png)
比如2019.12.30~31有二天，少于4天，所以这一周属于2020年，则使用YYYY显示时会出现错误；
```oc
NSDateFormatter *f1 = [NSDateFormatter new];
[f1 setDateFormat:@"yyyy-MM-dd"];
NSDateFormatter *f2 = [NSDateFormatter new];
[f2 setDateFormat:@"YYYY-MM-dd"];
NSDate *d1 = [f1 dateFromString:@"2016-01-01"];
NSString *d1string = [f2 stringFromDate:d1];
NSLog(@"%@", d1string);
NSDate *d2 = [f1 dateFromString:@"2019-12-30"];
NSString *d2String = [f2 stringFromDate:d2];
NSLog(@"%@", d2String);
```
打印结果：
```
2015-01-01
2019-12-30
Program ended with exit code: 0
```

#### 如何设置日历以星期一开始
mac及iPhone上默认以星期日开始，可以调整成国际标准，这样可以更简单的看出YYYY存在的问题；

mac上设置，通用→ 语言与地区， 每周的第一天，默认为以星期日开始；

iPhone上设置→ 日历→ 星期开始于，默认以星期日开始

***

[关于日期格式你必须知道的坑](https://blog.csdn.net/qq_37358143/article/details/103868657)
