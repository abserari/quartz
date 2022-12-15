---
title: "Kubernetes 的集成测试"
date: "2022-10-28"
aliases: []
---
## 摘要


## 问题、提示
- Kubernetes 的[[集成测试]]怎么做的？
- 如何让自己的应用在 Kubernetes 中进行集成测试？ #query 
	- [[Kind]] 可能就是答案。Refer [Running KIND Inside A Kubernetes Cluster For Continuous Integration | D2iQ](https://d2iq.com/blog/running-kind-inside-a-kubernetes-cluster-for-continuous-integration)

## 主要笔记
- 模块依赖：最依赖的两个模块分别是 ETCD 和 APIServer 
	- ETCD：Kubernetes 集成测试需要安装 [[etcd]]（只要安装即可，不需要启动），测试的时候通过 [[Golang]] 调用 Command 执行二进制程序的方式启动。
		- [来自源码](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/test/integration/framework/etcd.go?L129&subtree=true#tab=def)
	- [[APIServer]] 是集成测试中必要且复杂的模块，[[StartTestServer]] 函数初始化了一些本地测试需要的配置，然后通过 [/](https://sourcegraph.com/github.com/kubernetes/kubernetes)[cmd /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd)[kube-apiserver /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kube-apiserver)[app /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/cmd/kube-apiserver/app)[server.go](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/cmd/kube-apiserver/app/server.go) 目录的[app.CreateKubeAPIServer](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/test/integration/framework/test_server.go?L168) 创建了一个本地测试服务器，这样也就启动了一个集成测试环境啦。
		- [启动来源代码](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/test/integration/framework/test_server.go)
- 项目结构组织：[/](https://sourcegraph.com/github.com/kubernetes/kubernetes)[test /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/test)[integration /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/test/integration) 在这个目录下存放集成测试的代码，以测试的对象分类（如 [[Deployment]], [[ConfigMap]]，[[Kubernetes Client|Client]]），用于复用的测试框架在文件夹 [framework](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/test/integration/framework) 内，如 [ETCD](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/test/integration/framework/etcd.go?L68) 和 [APIServer](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/test/integration/framework/test_server.go) 的启动都是该框架的函数调用。
	- 这样的集成测试框架很有用，比如开发 [[Kubernetes]] 的 [[Operator]] 的时候，[`controller-runtime`](http://sigs.k8s.io/controller-runtime) 提供 `envtest` ([godoc](https://pkg.go.dev/sigs.k8s.io/controller-runtime/pkg/envtest?tab=doc)) 帮助设置并启动的 [[controllers]] 实例来写集成测试，不需要 [[kubelet]]，[[controller-manager]] 或者其他组件。
- 触发方式：通过 `makefile` 的脚本触发，具体执行操作在：[/](https://sourcegraph.com/github.com/kubernetes/kubernetes)[hack /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/hack)[make-rules /](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/tree/hack/make-rules)[test-integration.sh](https://sourcegraph.com/github.com/kubernetes/kubernetes/-/blob/hack/make-rules/test-integration.sh)
- [[etcd 的集成测试]]