---
title: golang协程
date: 2020-08-05 15:36:09
tags:
- 协程
- 多线程
categories:
- golang
---

协程可以理解为一种用户态的轻量级线程。协程类似于线程，但与线程有区别。协程并不受操作系统内核控制而是完全由程序所控制，即在用户态执行。例如在生产者-消费者模型中，可以由用户决定在什么时候暂停程序。

# golang的协程

## golang的协程的优点

1. 相比线程而言，Go 协程的成本极低。堆栈大小只有若干 kb，并且可以根据应用的需求进行增减。而创建线程必须指定堆栈的大小，其堆栈是固定不变的。
2. Go 协程会复用（Multiplex）数量更少的 OS 线程。即使程序有数以千计的 Go 协程，也可能只有一个线程。如果该线程中的某一 Go 协程发生了阻塞（比如说等待用户输入），那么系统会再创建一个 OS 线程，并把其余 Go 协程都移动到这个新的 OS 线程。所有这一切都在运行时进行
3. Go 协程使用信道（Channel）来进行通信。信道用于防止多个协程访问共享内存时发生竞态条件

<!-- more -->

## golang协程的例子

```golang
package main

import (
	"fmt"
	"time"
)

func numbers() {
	for i := 0; i < 6; i++ {
		time.Sleep(250 * time.Millisecond)
		fmt.Printf("%d\n", i)
	}
}

func words() {

	for i := 'a'; i <= 'e'; i++ {
		time.Sleep(400 * time.Millisecond)
		fmt.Printf("%c\n", i)
	}
	
}

func main() {
    //启动两个协程
	go numbers()
	go words()

	time.Sleep(3000 * time.Millisecond)
	fmt.Println("main function")

}
```

输出的效果和时间线类似于

![](Goroutines-explained.png)

# golang的信道

golang通过协程来实现并发，并且使用信道来在Go协程之间通信。使用`chan <T>`来表示信道的类型，使用`make`来定义信道。通过`data := <- a`来读取信道的值，通过`a <- data`来设置信道存放的值。

## 单向信道与双向信道

golang的信道可以只设置为

## 死锁
使用信道是要考虑的一个重要因素是死锁（Deadlock）。如果一个协程发送数据给一个信道，而没有其他的协程从该信道中接收数据，那么程序在运行时会遇到死锁，并触发 panic 。同样地，如果一个协程从一个信道中接收数据，而没有其他的协程向这个信道发送数据，那么程序同样造成死锁，触发 panic (使用`defer`处理)

>举一个简单的小例子通道类似一个水管，两边只要有一端被塞住就会形成死锁

### 死锁的处理


## golang使用信道的例子

```golang
package main

import (
	"fmt"
	"math"
)

func squares(number int, squaresOutput chan int) {
	sum := 0
	for number != 0 {
		digit := number % 10
		sum += int(math.Pow(float64(digit), 2))
		number /= 10
	}
	squaresOutput <- sum
}

func cubes(number int, cubeOutput chan int) {
	sum := 0
	for number != 0 {
		digit := number % 10
		sum += int(math.Pow(float64(digit), 3))
		number /= 10
	}
	cubeOutput <- sum
}
func main() {
	number := 589
	sqrch := make(chan int)
	cubech := make(chan int)
	go squares(number, sqrch)
	go cubes(number, cubech)
	// main函数将在此行被阻塞直到信道得到数据
	squares, cubes := <-sqrch, <-cubech
	fmt.Println("Final output", squares+cubes)
}
```



# 使用协程解决生产者消费者问题

相对于JAVA我们不用手动控制缓冲区

```golang
package main

import (
	"fmt"
	"time"
	"github.com/google/uuid"
)

type Product struct {
	No string
}

func (p Producer) work(Box chan Product) {
	for {
		product := Product{No: uuid.New().String()}
		Box <- product
		fmt.Printf("生产者-%d 生产了 产品-%s\n", p.No, product.No)
		time.Sleep(time.Millisecond * 200)
	}

}
c
type Producer struct {
	No int
}

type Comsumer struct {
	No int
}

func (c Comsumer) buy(Box chan Product) {
	for {
		product := <-Box
		fmt.Printf("消费者-%d 消费了 产品-%s\n", c.No, product.No)
		time.Sleep(time.Millisecond * 1000)
	}
}

func main() {
	// 创建缓冲区
	Box := make(chan Product, 1024)

	// 创建50个生产者
	for i := 1; i <= 50; i++ {
		p := Producer{No: i}
		go p.work(Box)
	}
	// 创建10个消费者
	for i := 1; i <= 10; i++ {
		c := Comsumer{No: i}
		go c.buy(Box)
	}

	// 防止提前退出
	time.Sleep(30 * time.Second)
}

```
