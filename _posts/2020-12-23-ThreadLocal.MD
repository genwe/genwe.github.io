---
layout: post
comments: true
title: ThreadLocal
---

聊聊ThreadLocal

对于iOS开发来说，runloop大家一定都有所了解，主线程自动开启主循环，子线程需要手动[NSRunloop getCurrentLoop]获取并启动run。即runloop是没有提供其他创建的方式，看过CF源码的人都知道，存放线程的方式是一个全局字典CFMutableDictionaryRef，以当前线程为key，以runloop实例对象为value。

如果有了解过java或安卓的looper，那一定知道这个全局的字段对应的就是ThreadLocal。

### ThreadLocal是什么？
JDK1.2提供的java.lang.ThreadLocal，是每个线程Thread的对立局部变量，可以用来解决线程并发问题，比使用同步锁机制更简单、方便。

### 常见的使用场景
最常见的ThreadLocal使用场景为用来解决数据库链接、session管理等

待补充...

***
### 引用源
+ []()
 

