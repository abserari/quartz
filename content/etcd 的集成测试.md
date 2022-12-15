---
title: "etcd 的集成测试"
date: "2022-10-28"
aliases: []
---
## 摘要
[[etcd]]

## 问题、提示
-  

## 主要笔记
-  一个 todo 改进：etcd 的检查泄露的 [[goroutine]] 方法需要重构为 [[uber]] 的 [go-leak](https://github.com/uber-go/goleak) 
	- [源码](https://sourcegraph.com/github.com/etcd-io/etcd/-/blob/client/pkg/testutil/leak.go?L19&subtree=true)
- 项目结构：etcd 的集成测试也同样有一个 [/](https://sourcegraph.com/github.com/etcd-io/etcd)[tests /](https://sourcegraph.com/github.com/etcd-io/etcd/-/tree/tests)[framework](https://sourcegraph.com/github.com/etcd-io/etcd/-/tree/tests/framework) 目录下的 integration 文件夹。
- 每次测试初始化使用 [`NewCluster(t testutil.TB, cfg *ClusterConfig) *Cluster`](https://sourcegraph.com/github.com/etcd-io/etcd/-/blob/tests/framework/integration/cluster.go?L117) 启动一个 etcd 集群用于集成测试，集成测试的入口是 [IntegrationTestRunner](https://sourcegraph.com/github.com/etcd-io/etcd/-/blob/tests/framework/framework.go?L24)。
- 在 framework 中有区分[Test Runner](https://sourcegraph.com/github.com/etcd-io/etcd/-/blob/tests/framework/framework.go?L24)：不同的 runner 代表测试场景的不同，有以下区别
	- UnitTestRunner：专门为单元测试准备的 runner，如果在这种测试入口中创建集群，则会失败（通过 \*[[testing.M]] 控制的）
	- E2eTestRunner：E2E 测试会真实运行 etcd 和 etcdctl 的二进制文件作为两个进程，用于模拟真实用户的操作来测试。
	- IntegrationTestRunner 通过使用内部库的代码，在多个 goroutine 上创建 etcd 集群，通信方式是 client 库。以此运行集成测试。
- 集成测试触发方式：Makefile，运行 `make test-integration` 触发	`PASSES="integration" ./scripts/test.sh $(GO_TEST_FLAGS)`
	- 无独有偶：etcd 的实际执行也在自己写的脚本里[/](https://sourcegraph.com/github.com/etcd-io/etcd)[scripts /](https://sourcegraph.com/github.com/etcd-io/etcd/-/tree/scripts)[test.sh](https://sourcegraph.com/github.com/etcd-io/etcd/-/blob/scripts/test.sh)，经过阅读 shell 判断，脚本中主要做了各种自定义参数和环境变量的传递，相当于原版 go test 的增强，
		- 比如添加了三个测试模式可以选择
			- 并行 parallel
			- 报错马上退出 keep_going
			- 全部运行最后收集报错 fail_fast
