---
title: "kubeadm 初始化流程"
date: "2022-10-19"
aliases: []
---
## 摘要


## 问题、提示
-  

## 主要笔记
- 代码位置： 在 Kubernetes 源码包里`kubernetes/cmd/kubeadm/app`，kubelet 这个 repo 只是用来做聚合 issues 用的。
- [sourcegraph 代码位置](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/cmd/kubeadm/app/cmd/init.go)
- kubeadm init 由一系列的 workflow 脚本组成，[截取如下](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/cmd/kubeadm/app/cmd/init.go?L141)：
	 ```go
	// initialize the workflow runner with the list of phases
	initRunner.AppendPhase(phases.NewPreflightPhase())
	initRunner.AppendPhase(phases.NewCertsPhase())
	initRunner.AppendPhase(phases.NewKubeConfigPhase())
	```
	- 每一个 Phase 内部都是一系列的 Golang 脚本，通过面向对象的设计，用包的调用操作函数。
		- 当然，底层的脚本调用通过[接口实现](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/cmd/kubeadm/app/util/initsystem/initsystem.go?L17)的方式，兼容了不同系统的调用启动，比如 [[Unix]] 系统上有 [[systemctl]] 就用，或者用 [[openrc]]。
		- kubeadm 自己写了一个遍历添加脚本的代码用作工作流调度：[/](https://sourcegraph.com/github.com/kubernetes/kubernetes)[cmd /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd)[kubeadm /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kubeadm)[app /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kubeadm/app)[cmd /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kubeadm/app/cmd)[phases /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kubeadm/app/cmd/phases)[workflow /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kubeadm/app/cmd/phases/workflow)[runner.go](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/cmd/kubeadm/app/cmd/phases/workflow/runner.go)
- [官方文档对于实现细节的展示](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/implementation-details/#core-design-principles)