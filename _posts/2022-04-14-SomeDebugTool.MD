---
layout: post
comments: true
title: iOS常见的调试工具
---

#### 结论：现行公司项目里有集成DoraemonKit、LookinServer，可以作为调试程序的补充手段
知道的老鸟请移步，下面的水文大概要浪费2~3分钟时间阅读！

不清楚大家有没有遇到这样的场景，测试说这边UI显示的不对，问怎么回事？很尴尬，测试的手机一般情况下没有开发证书，没法直接用XCode调试，并且有些场景可能重新跑程序的时候就不好复现了。。。或者自己业务的界面偶现的情况下多了点东西，也不知道是谁负责的部分。。。

我就遇到这样的情况，用的动态action(公司的一套hook接口做预埋行为的库，比如弹框等行为)，测试问测试机上弹窗怎么不太一样，尬了，以前没接触过动态action，找了一下文档、wiki也没发现哪里有介绍具体怎么实现的，这个时候，如果我能知道这个弹框界面类名是啥就好了。。。

如果有遇到类似的问题，下面两个工具可能会帮到你

#### DoKit
[DoKit官网](http://www.dokit.cn/#/index/home)

![_config.yml](/images/DoKit.png)

可以通过“视觉工具->组件检查”这个工具来查看相关UI的类名

工具集成了很多功能，当然现行工程也只集成了一部分，还有其他功能，有兴趣的话，了解一下

***

胡思乱想：

如果能直接把手机上的日志发到电脑上解析好，那就很赞了

遇到的真实场景，某个下午我试了很多次，日志死活上传不到平台上，至少试了一下6～7次，重装、卸载、换账号都试过了，后来我放弃了

DIDI工具集合 -> 沙盒浏览器 -> 找到日志 长按分享 隔空投送，日志文件有了，哪位大佬有时间再顺手写个解析工具就完美了，当然，如果能在Debug选项里加一个一键导出日志就更好了。。。


#### Lookin
[Lookin官网](https://lookin.work)

![_config.yml](/images/Lookin.png)

做iOS的同学应该都听说过一款叫做Reveal的软件，个人感觉Lookin比Reveal更强大，不仅仅支持UI图层等查看，还支持直接查看指定对象的属性和方法，监听方法的调用，非常赞！关键是免费、免费、免费！！！

如果有越狱机的话，按个lookin的插件，完全可以替代Reveal用

前段时间一直以为是我测试机的问题，XCode上的UI View Hierarchy工具用不了了，一直靠着Lookin才苟延残喘了一段时间！前几天找同事调试问题，又试了一把XCode的功能，居然在升级XCode之后又能用了。。。 

前几天遇到个情况，测试怀疑某界面上字体存在自动缩放的情况，说显示的字号不对，当时工程没有开启，不必要的时候，也不想干等着打开运行工程了，就用了一下Lookin，当时显示内容的地方还不是Label，而是YYLabel之类的View，没法直接看到字体字号是多少，盲猜了一下其也有fontSize之类的属性，用了一下控制台功能，还是蛮给力的

***
#### 实时查看手机日志
前段时间遇到个问题，问题出现在app关闭之后，在我的测试机上没法复现(iOS13.6)，自己手机上可以复现(iOS15.3)，但自己的手机没开发证书，好像只能通过日志去定位，当时没想起来查看手机的日志，而是通过公司的平台去查看上传的日志，效率可想而知的低。。。，用的很少的情况下，都忘了怎么查看手机上的日志了。。。

XCode -> Window -> Devices and Simulators -> View Device Logs 

或者用libimobiledevice等工具，聊胜于无吧

如果有越狱机的话，libimobiledevice用来转换端口，还是蛮好用的

***
### 引用源
+ [DoKit官网](http://www.dokit.cn/#/index/home)
+ [Lookin官网](https://lookin.work)
 
