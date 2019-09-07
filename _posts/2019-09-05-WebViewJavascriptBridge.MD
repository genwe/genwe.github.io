---
layout: post
comments: true
title: 读WebViewJavascriptBridge源码
---

读WebViewJavascriptBridge源码

## JS代码写在.m文件里没报错
```c
#define __wvjb_js_func__(x) #x
```

使用了一个简单的宏写法， 避免了直接写成@"XXX"形式格式显示问题

## JS匿名函数前加;/!的原因
分号开头是为了JS代码合并压缩的时候和其他函数分割开；（JS文件结束处是没有分号的，若几个JS连在一起时可能发生语法混淆；分号用于分割，相当于空语句）
感叹号是用于立即执行函数（也可以换成+/-/!/~，相当于加了一层小括号）

闭包的写法将WebViewJavascriptBridge对象暴露出来，其他的内部数据及方法都被封装

## 如果一个类在不同情况下可能遵守不同的协议，如何封装这样的差异 
条件编译可以封装不同平台Api协议带来的差异

## 如何封装UIWebView 和 WKWebView 执行JS方法的差异
库中采用 UIWebView 方法 stringByEvaluatingJavaScriptFromString: 执行JS（是一个同步方法）;

WKWebView 方法 evaluateJavaScript: completionHandler:执行JS(异步方法)

通过WebViewJavascriptBridgeBaseDelegate协议

## 这个库怎么用？
WebViewJavascriptBridge 提供的方法简略后如下：
```objc
/// 初始化
+ (instancetype)bridgeForWebView:(id)webView;
/// 注册监听
- (void)registerHandler:(NSString*)handlerName handler:(WVJBHandler)handler;
/// 移除监听
- (void)removeHandler:(NSString*)handlerName;
/// 调用JS方法
- (void)callHandler:(NSString*)handlerName data:(id)data responseCallback:(WVJBResponseCallback)responseCallback;
/// 设置webView的代理
- (void)setWebViewDelegate:(id)webViewDelegate;
```
提供的方法主要有 初始化、设置webView的代理、注册（添加）/移除 监听、调用JS方法；

库提供的例子，使用方式如下：
```objc
    _bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
    [_bridge setWebViewDelegate:self];
    
    [_bridge registerHandler:@"testObjcCallback" handler:^(id data, WVJBResponseCallback responseCallback) {
        NSLog(@"testObjcCallback called: %@", data);
        responseCallback(@"Response from testObjcCallback");
    }];
    
    [_bridge callHandler:@"testJavascriptHandler" data:@{ @"foo":@"before ready" }];
```
当前控制器强引用bridge对象、强引用webView，bridge通过初始化及设置代理，弱引用了当前控制器、webView;

注册（添加）/移除监听方法，通过方法名是否可以得到一些推论？添加/移除时需要提供字符串handlerName参数（key），bridge应该有个集合型属性（可以注册多个方法），用来保存相对应的block（NSMutableDictionary）;

调用JS方法，参数有方法名、需要传递的参数值、回调；

Web端使用方式如下：
```js
function setupWebViewJavascriptBridge(callback) {
  if (window.WebViewJavascriptBridge) { return callback(WebViewJavascriptBridge); }
  if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback); }
  window.WVJBCallbacks = [callback];
  var WVJBIframe = document.createElement('iframe');
  WVJBIframe.style.display = 'none';
  WVJBIframe.src = 'https://__bridge_loaded__';
  document.documentElement.appendChild(WVJBIframe);
  setTimeout(function() { document.documentElement.removeChild(WVJBIframe) }, 0)
}

setupWebViewJavascriptBridge(function(bridge) {
  var uniqueId = 1

  bridge.registerHandler('testJavascriptHandler', function(data, responseCallback) {
    var responseData = { 'Javascript Says':'Right back atcha!' }
    responseCallback(responseData)
  })

  document.body.appendChild(document.createElement('br'))

  var callbackButton = document.getElementById('buttons').appendChild(document.createElement('button'))
  callbackButton.innerHTML = 'Fire testObjcCallback'
  callbackButton.onclick = function(e) {
    e.preventDefault()
    bridge.callHandler('testObjcCallback', {'foo': 'bar'}, function(response) {
    })
  }
})
```
window下挂了WebViewJavascriptBridge（web环境下的bridge）、WVJBCallbacks（callback数组）两个对象

查看WebViewJavascriptBridge_JS.m :
```js
window.WebViewJavascriptBridge = {
  registerHandler: registerHandler,
  callHandler: callHandler,
  _fetchQueue: _fetchQueue,
  _handleMessageFromObjC: _handleMessageFromObjC
};
```
暴露给使用者的只有registerHandler、callHandler

看上面的例子中setupWebViewJavascriptBridge
```js
WVJBIframe.src = 'https://__bridge_loaded__';
```
src属性设置会调用decidePolicyForNavigationAction方法，方法中判断LoadedURL后调用injectJavascriptFile方法，注入bridge对象

看Native 与 web 环境的 bridge 方法都有回调，比如原生按钮点击调用了JS的方法，等JS方法调用完成后，需要把结果再传回原生的回调方法中，整个通信机制是怎么处理的？

先看看双方bridge各自的属性：

> WebViewJavascriptBridgeBase.h
```objc
@property (weak, nonatomic) id <WebViewJavascriptBridgeBaseDelegate> delegate;
/// 用于保存实时会话过程中需要发送给JS环境的消息
@property (strong, nonatomic) NSMutableArray* startupMessageQueue;
/// 用于保存原生与JS环境互相调用的回调模块
@property (strong, nonatomic) NSMutableDictionary* responseCallbacks;
/// 用于保存Native环境注册的方法，key为方法名，value为对应回调的block;
@property (strong, nonatomic) NSMutableDictionary* messageHandlers;
@property (strong, nonatomic) WVJBHandler messageHandler;
```

> WebViewJavascriptBridge_JS.m
```js
var messagingIframe;
/// 用于存储消息列表
var sendMessageQueue = [];
/// 用于存储JS注册的函数
var messageHandlers = {};
/// OC调用JS的回调
var responseCallbacks = {};
```
JS调用OC，(JS环境) callHandler -> _doSend(如果有回调的话，需要responseCallbacks中保存当前的responseCallback)->(message对象队列) | | (Native环境) 代理方法 -> flushMessageQueue -> (message字典数组)

WVJBMessage/message对象 有四个key,  handlerName、callbackId、responseId、responseData；



## 库中JS代码通过什么方式主动调用Native的？
通过先在DOM中添加一个隐藏的iframe，然后改变其src属性值时会自动调用相关联的Native代理方法



## 引用源
+ [https://blog.csdn.net/happydecai/article/details/79289223#](https://blog.csdn.net/happydecai/article/details/79289223#)
+ [https://juejin.im/entry/58e4a76a44d904006d2a7778](https://juejin.im/entry/58e4a76a44d904006d2a7778)
+ [https://blog.csdn.net/mociml/article/details/47701133](https://blog.csdn.net/mociml/article/details/47701133)
 
