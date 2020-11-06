---
title: 在C程序中使用汇编
date: 2020-08-07 21:47:13
tags:
- C
- 汇编
categories:
- C
---

# 使用场景

- 即使是C语言也无法操作的硬件，如寄存器
- 使用C后性能依旧不够,需要使用汇编进行优化
<!-- more -->
# 使用方式

## 使用内联汇编

在gcc中使用关键字`asm`以及它的宏定义`__asm__`来声明内联汇编表达式,在实际使用中最好使用`__asm__`。在编译时使用`-masm`参数来指定汇编所用的方言

### 可选项:

- `volatile` 对于任何asm块都是隐式选择`volatile`
- `inline` 会尽可能的减小汇编块所占大小

### 汇编代码块

一串字符串形式的指令，GCC不会自行解析汇编程序指令，也不知道它们的含义，甚至不知道它们是否是有效的汇编程序输入。

### 一个简单的例子

```c
#define DebugBreak() __asm__("int $3")
```
C语言与汇编混编的扩展使用参考[这里](https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html#Extended-Asm)

## 调用汇编函数

函数调用的方式是固定的，所以只要以符合调用约定来编写汇编，即可使用C语言调用。

### 调用约定
可以在linuxX64平台中使用`syscall`指令，函数调用时与以下寄存器相关

| 寄存器 |           功能            |
| :----: | :-----------------------: |
|  rax   | 系统调用号,也是返回值地址 |
|  rdi   | 函数调用传递的第一个参数  |
|  rsi   | 函数调用传递的第二个参数  |
|  rdx   | 函数调用传递的第三个参数  |
|  rcx   | 函数调用传递的第四个参数  |
|   r8   | 函数调用传递的第五个参数  |
|   r9   | 函数调用传递的第六个参数  |

当多于六个参数时,从右向左放入栈中来进行传递。

### 例子

add.S文件

```assembly
	.text
	.globl	add
	.type	add, @function
add:
    leal (%rdi,%rsi),%eax
    ret
```

main.c文件

```c
#include <stdio.h>
extern int add(int, int);
int main(int argc, char const *argv[])
{
    printf("%d", add(4, 6));
    return 0;
}
```

编译命令

```bash
gcc main.c add.S -o main
```

