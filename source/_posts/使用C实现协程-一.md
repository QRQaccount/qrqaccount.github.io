---
title: 使用C实现协程(一)
date: 2020-08-06 17:42:15
tags:
- C
- 协程
categories:
- 使用C实现协程
---

文章参考[轮子哥的教程](https://zhuanlan.zhihu.com/p/25993392)以及[风云的博客](https://blog.codingnow.com/2012/07/c_coroutine.html)<br>可以对C/C++协程有个大概了解:[Why c++ coroutine？Why libgo？](https://my.oschina.net/yyzybb/blog/1817226)

# 为什么要使用协程

协程切换的代价更低,速度更快(ns级别)，协程所占用的资源更少。协程的切换完全在用户状态下进行,线程的切换需要在内核空间完成(两次内核态与用户态的切换是主要时间开销)。协程切换所做的事情较少，只涉及基本的CPU上下文切换<br><br>对于切换所做的事,以libco协程切换汇编为例(x86_64部分),附带注释
<!-- more -->
C函数接口:

```c
void coctx_swap( coctx_t *,coctx_t* ) asm("coctx_swap");
```
coctx_t结构体定义:

```c
struct coctx_t {
    void *regs[ 14 ]; 
    size_t ss_size;
    char *ss_sp;
};
```

汇编部分:
```x86asm
	leaq 8(%rsp),%rax   ;%rsp是第一个协程控制块所对应的地址
	leaq 112(%rdi),%rsp ;将第一个参数的值赋值给%rsp寄存器
                        ;即将第一个参数指向的coctx_t结构体中的regs数组作为栈空间使用

    ;将第一个协程控制块需要保存的数据依次压入堆栈
	pushq %rax  
	pushq %rbx
	pushq %rcx
	pushq %rdx
    pushq -8(%rax) ;ret func addr
	pushq %rsi
	pushq %rdi
	pushq %rbp
	pushq %r8
	pushq %r9
	pushq %r12
	pushq %r13
	pushq %r14
	pushq %r15
	

	movq %rsi, %rsp ;将第二个参数指向的coctx_t结构体中的regs数组作为一个堆栈使用

    ;将第二个协程控制块中的数据依次取出放入CPU寄存器继续上次的任务
	popq %r15
	popq %r14
	popq %r13
	popq %r12
	popq %r9
	popq %r8
	popq %rbp
	popq %rdi
	popq %rsi
	popq %rax ;ret func addr
	popq %rdx
	popq %rcx
	popq %rbx
	popq %rsp

    ;转移到返回地址处开始执行
	pushq %rax
    xorl %eax, %eax
	ret
```

# 需要实现的功能

需要实现
- 有栈协程
- 使用非共享栈,每个协程创建自己独立的栈
- 实现协程之间的通信
- 初步使用ucontext系列函数,之后再基于x86_64汇编实现上下文交换函数
> {% post_link 在C程序中使用汇编 %}

尝试实现
- 基于协程实现一套网络IO库(参考[libmill](https://github.com/sustrik/libmill))

# 用户态与内核态的切换(扩展)

​	以linux系统为例,为了减少对有限资源的访问和冲突,对不同的操作赋予不同的执行等级.也就是所谓的特权概念.一些关键性的操作必须由最高权限等级的程序来完成.例如：Intel x86 CPU 具有四种不同的执行级别 RING0, RING1, RING2, RING3，Linux  操作系统只使用了其中的 RING0 和 RING3 分别表示内核态与用户态。处于 RING3 状态的用户态代码不能直接访问处于 RING0  的内核态代码的地址空间(包括程序和数据).程序运行时,当需要操作系统来帮助完成某些它没有的权限的的工作时就会切换到内核态.

​	例如:用户运行一个程序，该程序所创建的进程开始是运行在用户态的，当程序要进行文件操作,网络数据发送等操作,必须通过系统调用,这些系统调用会调用内核中的代码来完成操作.这时,必须切换到Ring0,然后进入3GB-4GB中的内核地址空间去执行这些代码完成操作.完成后,切换回到用户态

## 用户态与内核态转换的三种方式

- 系统调用
- 异常
- 外围设备中断

## linux中的线程模型

linux中使用的是一对一模型，每一个用户线程映射到一个相应的内核线程。所以由用户态到内核态的切换可以理解为由用户线程到相应的内核线程的切换。