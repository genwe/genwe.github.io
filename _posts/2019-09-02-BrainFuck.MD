---
layout: post
comments: true
title: BF简介
---

简单介绍一下BF

## BF(BrainFuck) 是什么？
是一种由8种操作符组成的图灵完备的编程语言

| 操作符 | 含义                   |
| ---   | ----                |
| >     | 当前指针+1             | 
| <     | 当前指针-1             |
| +     | 指针指向的地址的字节的值+1 |
| -     | 指针指向的地址的字节的值-1      |
| .     | 输出当前指针指向的地址的字节内容（ASCII码）  |
| ,     | 输入内容到当前指针指向的字节单元（ASCII码）      |
| [     | 如当前指针指向地址的字节单元值为0，则向后跳转到对应]指令的下个指令位置|
| ]     | 如当前指针指向地址的字节单元值不为0，则向前跳转到对应[指令的下个指令位置|

---

相关指令翻译为C语言（假设ptr是char*类型）

| BF    | C                   |
| ---   | ----                |
| >     | ++ptr;             | 
| <     | --ptr;             |
| +     | ++*ptr;           |
| -     | --*ptr;            |
| .     | putchar(*ptr);    |
| ,     | *ptr = getchar();  |
| [     | while(*ptr) {     |
| ]     | }                 |




## C语言版解释器
```c
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

#define TOKENS "+-<>,.[]"

#define CODE_SEGMENT_SIZE 30000
#define STACK_SEGMENT_SIZE 1000
#define DATA_SEGMENT_SIZE 30000

typedef void (*Callback)(void);

struct {
    char cs[CODE_SEGMENT_SIZE];
    long ip;
    
    char ss[STACK_SEGMENT_SIZE];
    long sp;
    
    char ds[DATA_SEGMENT_SIZE];
    long bp;
    
    Callback fn[128];
}vm;

void vm_forward() {
    vm.bp = (vm.bp + 1) % DATA_SEGMENT_SIZE;
}

void vm_backward() {
    vm.bp = (vm.bp + DATA_SEGMENT_SIZE - 1) % DATA_SEGMENT_SIZE;
}

void vm_increment() {
    vm.ds[vm.bp]++;
}

void vm_decrement() {
    vm.ds[vm.bp]--;
}

void vm_input() {
    vm.ds[vm.bp] = getchar();
}

void vm_output() {
    putchar(vm.ds[vm.bp]);
}

void vm_while_entry() {
    if (vm.ds[vm.bp]) {
        vm.ss[vm.sp] = vm.ip - 1;
        vm.sp++;
    } else {
        int c = 1;
        for (vm.ip++; vm.cs[vm.ip] && c; vm.ip++) {
            if (vm.cs[vm.ip] == '[') {
                c++;
            } else if (vm.cs[vm.ip] == ']') {
                c--;
            }
        }
    }
}

void vm_while_exit() {
    if (vm.ds[vm.bp]) {
        vm.sp--;
        vm.ip = vm.ss[vm.sp];
    }
}

void setup() {
    int c;
    int i;
    
    memset(&vm, 0, sizeof(vm));
    vm.fn['>'] = vm_forward;
    vm.fn['<'] = vm_backward;
    vm.fn['+'] = vm_increment;
    vm.fn['-'] = vm_decrement;
    vm.fn[','] = vm_input;
    vm.fn['.'] = vm_output;
    vm.fn['['] = vm_while_entry;
    vm.fn[']'] = vm_while_exit;
    
    for (i = 0; (c = getchar()) != EOF;) {
        if (strchr(TOKENS, c)) {
            vm.cs[i] = c;
            i++;
        }
    }
}

void run() {
    while (vm.cs[vm.ip]) {
        vm.fn[vm.cs[vm.ip]]();
        vm.ip++;
    }
}

int main(int argc, char *argv[]) {
    
    if (argc > 1) {
        freopen(argv[1], "r", stdin);
    }
    setup();
    run();
    return 0;
}

```
helloworld.bf
```
++++++++++[>+++++++>++++++++++>+++>+<<<<-]>++.>+.+++++++..+++.>++.<<+++++++++++++++.>.+++.------.--------.>+.>.
```

## 引用源
+ [https://blog.csdn.net/redraiment/article/details/7483062](https://blog.csdn.net/redraiment/article/details/7483062)
 
