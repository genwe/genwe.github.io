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

#define STRING(x) #x
STRING(hello) === "hello"

+ (#@)（Charzing Operator）宏变量左右各加单引号；

+ （++）(Token-pasting Operator)拼接符号

## 宏定义中do{}while(0)的作用
使用do{}while(0)包裹起立，成为一个独立的语法单元，不会与上下文发生混淆（比如出现多个分号的问题）;同时因为绝大多数的编译器都能识别do{}while(0)这种无用的循环并进行优化，所以使用这种方式不会导致程序的性能降低；

## 如何在OC中使用宏添加字符串枚举类型？

## 字符串枚举其他的方式
.h 文件中 -------------
```
typedef NSString *KLTypeStr NS_STRING_ENUM;

FOUNDATION_EXPORT KLTypeStr const KLTypeStringRed;
FOUNDATION_EXPORT KLTypeStr const KLTypeStringGreen;
FOUNDATION_EXPORT KLTypeStr const KLTypeStringOrange;
```
.m 文件中 --------------
```
NSString * const KLTypeStringRed = @"红色";
NSString * const KLTypeStringGreen = @"绿色";
NSString * const KLTypeStringOrange = @"橘色"; 
```
比较的时候 StringEnum1 == StringEnum2 直接比较的是内存地址，效率会更高。
相比会产生过多二进制文件的宏定义方式,假如宏定义比较多,建议用FOUNDATION_EXPORT


## 引用源
+ [https://www.cnblogs.com/Anker/p/3418792.html](https://www.cnblogs.com/Anker/p/3418792.html)


  
