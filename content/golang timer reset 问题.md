---
title: "golang timer reset 问题"
date: "2022-11-11"
aliases: []
---
## 摘要
定时器创建后是单独运行的，超时后会向通道写入数据，你从通道中把数据读走。**当前一次的超时数据没有被读取，而设置了新的定时器，然后去通道读数据，结果读到的是上次超时的超时事件，看似成功，实则失败**

所以使用 Reset 的时候，
```go
if !t.Stop() {
	<-t.C 
} 
t.Reset(d)
```
然而这种方式会遇到问题 [[time: Timer.Reset is not possible to use correctly #14038]]，即如果计时器已经过期，且 channel 被取走数据，这个时候不要先调用 Stop()。

如果通过变量标明是否取出了数据，又会有竞态的问题，比如 goroutine 顺序完全依赖于调度器调度。
综上，没有 Reset() 完全理想的正确使用方式。

## 问题、提示
-  

## 主要笔记
-  Refer： [论golang Timer Reset方法使用的正确姿势 | Tony Bai](https://tonybai.com/2016/12/21/how-to-use-timer-reset-in-golang-correctly/)
- Golang官方有关Timer的issue list：
	- runtime: special case timer channels #8898  
	- time:timer stop ,how to use? #14947  
	- time: document proper usage of Timer.Stop #14383  
	- *time: Timer.Reset is not possible to use correctly #14038  
	- Time.After doesn’t release memory #15781  
	- runtime: timerproc does not get to run under load #15706  
	- time: time.After uses memory until duration times out #15698  
	- time:timer stop panic #14946  
	- *time: Timer.C can still trigger even after Timer.Reset is called #11513  
	- time: Timer.Stop documentation incorrect for Timer returned by AfterFunc #17600

