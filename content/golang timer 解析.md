---
title: "golang timer 解析"
date: "2022-10-21"
aliases: []
---
网址： https://tonybai.com/2016/12/21/how-to-use-timer-reset-in-golang-correctly/

近期[gopheracademy blog](https://blog.gopheracademy.com/)发布了一篇 《[How Do They Do It: Timers in Go](https://blog.gopheracademy.com/advent-2016/go-timers)》，通过对timer源码的分析，讲述了timer的原理，大家可以看看。

go runtime 实际上仅仅是启动了一个单独的 [[goroutine]]，运行 timerproc函数，维护了一个”[[最小堆]]”，定期wake up后，读取堆顶的timer，执行timer对应的f函数，并移除该timer element。创建一个Timer实则就是在这个最小堆中添加一个element，Stop一个timer，则是从堆中删除对应的element。