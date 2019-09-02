---
layout: post
comments: true
title: C/OC中宏的简单使用
---

简单介绍一下C/OC中宏的使用

## C语言 宏是什么？
![_config.yml](/images/gcc_make_step.jpeg)

一般来说，C源代码到目标程序到执行编译需要四个步骤：预处理->编译->汇编->链接；
宏，预处理命令的一种，它允许用一个标示符来表示一个字符串；

## C语言 宏与函数的区别
C语言的 宏在预处理阶段会被编译器替换掉，而且一般只能做简单的文本替换，编译器对其不做任何的语法检测（没有类型系统）；

## 特殊符号：#、#@、##
+ （#） （Stringizing Operator）将其后面的宏参数进行字符串化操作，简单的说就是在它引用的宏变量的左右各加一个双引号；
```c
#define STRING(x) #x
STRING(hello) ===〉 "hello"
```
+ (#@)（Charzing Operator）宏变量左右各加单引号；
```c
#define STRING(x) #@x
STRING(hello) ===〉 'hello'
```
+ （++）(Token-pasting Operator)拼接符号

## 宏定义中do{}while(0)的作用
使用do{}while(0)包裹起立，成为一个独立的语法单元，不会与上下文发生混淆（比如出现多个分号的问题）;同时因为绝大多数的编译器都能识别do{}while(0)这种无用的循环并进行优化，所以使用这种方式不会导致程序的性能降低；

## 如何在OC中使用宏添加字符串枚举类型？
 EnumMarcos.h ----
 ```c
 #ifndef EnumMarcos_h
#define EnumMarcos_h

#pragma mark - Enum Factory Macros

/**
 定义一个枚举case
 */
#define ENUM_VALUE(name, assign) name assign,

/**
 展开后是一个switch语句的case，返回一个字符串
 */
#define ENUM_CASE(name, assign) case name: return @#name;

/**
 展开后通过比较字符串，返回相应枚举case
 */
#define ENUM_STRCMP(name, assign) if ([string isEqualToString:@#name]) return name;

/**
 定义枚举，声明反射函数；
 */
#define DECLARE_ENUM(EnumType, ENUM_DEF) \
typedef NS_ENUM(NSInteger, EnumType) { \
ENUM_DEF(ENUM_VALUE) \
}; \
NSString *NSStringFrom##EnumType(EnumType value); \
EnumType EnumType##FromNSString(NSString *string); \

/**
 实现实现函数；
 */
#define DEFINE_ENUM(EnumType, ENUM_DEF) \
NSString *NSStringFrom##EnumType(EnumType value) \
{ \
switch(value) \
{ \
ENUM_DEF(ENUM_CASE) \
default: return @""; \
} \
} \
EnumType EnumType##FromNSString(NSString *string) \
{ \
ENUM_DEF(ENUM_STRCMP) \
return (EnumType)0; \
}

#endif /* EnumMarcos_h */
 ```
### 例子

1. 在任意.h文件中定义枚举，声明反射函数：

	```c
	#import "EnumMarcos.h"

	#define RAP_DIRECTION(XX) \
	XX(RAPDirectionEast, ) \
	XX(RAPDirectionSouth, ) \
	XX(RAPDirectionWest, = 50) \
	XX(RAPDirectionNorth, = 100) \

	DECLARE_ENUM(RAPDirection, RAP_DIRECTION)
	```

2. 在相应的.m文件中实现反射函数：

	```c
	DEFINE_ENUM(RAPDirection, RAP_DIRECTION)
	```

3. 尝试使用：

	```c
    NSString *str = NSStringFromRAPDirection(RAPDirectionEast);
    NSLog(@"RAPDirectionEast has case name: %@", str);
    
    
    RAPDirection dir = RAPDirectionFromNSString(@"RAPDirectionNorth");
    NSLog(@"RAPDirectionNorth has case value: %zd", dir);
	```

输出结果：

```
RAPDirectionEast has case name: RAPDirectionEast
RAPDirectionNorth has case value: 100
```


## 字符串枚举其他的方式
.h 文件中 -------------
```c
typedef NSString *KLTypeStr NS_STRING_ENUM;

FOUNDATION_EXPORT KLTypeStr const KLTypeStringRed;
FOUNDATION_EXPORT KLTypeStr const KLTypeStringGreen;
FOUNDATION_EXPORT KLTypeStr const KLTypeStringOrange;
```
.m 文件中 --------------
```c
NSString * const KLTypeStringRed = @"红色";
NSString * const KLTypeStringGreen = @"绿色";
NSString * const KLTypeStringOrange = @"橘色"; 
```
比较的时候 StringEnum1 == StringEnum2 直接比较的是内存地址，效率会更高。
相比会产生过多二进制文件的宏定义方式,假如宏定义比较多,建议用FOUNDATION_EXPORT

## Xcode中常用的预处理指令
1. 除了使用 #pragma mark -添加分割线之外, 其余的你有用过吗?
*     #pragma mark - 添加分割线
* 		/* FIXME: 添加标记2 */
* 		// MARK: 添加标记3
* 		/* !!!: 添加标记4 */
* 		/* ???: 添加标记5 */
* 		/* TODO: 添加标记6 */
* 		#warning 添加警告
2. swift的注释
*     // MARK: - 添加标记1   
*     // FIXME: 添加标记2    
*     /* FIXME: 添加标记2 */    
*     // MARK: - 添加标记3    
*     /* TODO: 添加标记6 */  

## NS_DESIGNATED_INITIALIZER 与 NS_UNAVAILABLE
在系统Api头文件和第三方框架中常见到这两个宏,DESIGNATED（指定）INITIALIZER（初始化）,是给初始化函数声明的后面加上一个编译器可见的标记，告诉调用者要去用哪个方法进行初始化，如果代码不够规范的时候，编译器将出现警告，帮忙找出初始化过程中可能存在的漏洞，增加代码的健壮性，保证整个子类的初始化过程可以覆盖到所有继承链上的成员变量得到合适的初始化；（一个类可以有多个指定初始化函数）
![_config.yml](/images/o_initializerDelegation01_2x.png)
OC的初始化规则：
1. 便利初始化函数只能调用自己类中的其他初始化方法；
2. 指定初始化函数才有资格调用父类的指定初始化函数
如果不想让调用者调用父类的某个初始化函数，可以用NS_UNAVAILABLE宏，如果调用者使用，则编译器会给出一个编译错误；

## 引用源
+ [https://www.cnblogs.com/Anker/p/3418792.html](https://www.cnblogs.com/Anker/p/3418792.html)
+ [https://github.com/Yannmm/OC-Enum-String-Convertible-Example](https://github.com/Yannmm/OC-Enum-String-Convertible-Example)
+ [https://blog.csdn.net/wm9028/article/details/49797929](https://blog.csdn.net/wm9028/article/details/49797929)
+ [https://www.cnblogs.com/smileEvday/p/designated_initializer.html](https://www.cnblogs.com/smileEvday/p/designated_initializer.html)
