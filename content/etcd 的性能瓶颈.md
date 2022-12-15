---
title: "etcd 的性能瓶颈"
tags:
---
[[etcd]] 通常和 [[API Server]] 一起作为整个 [[Kubernetes 的性能瓶颈]]


## Refer Link
[蚂蚁集团万级规模 K8s 集群 etcd 高可用建设之路 · SOFAStack](https://www.sofastack.tech/blog/ant-groups-10000-scale-k8s-cluster-etcd-high-availability-construction-road/) 以下摘抄的经验数据

	当 K8s 集群规模扩大时，etcd 承载着 KV 数据剧增、event 消息暴增以及消息写放大的三种压力。 为了证明所言非虚，特引用部分数据为佐证：
	1.  etcd KV 数据量级在 100 万以上；
	2.  etcd event 数据量在 10 万以上；
	3.  etcd 读流量压力峰值在 30 万 pqm 以上，其中读 event 在 10k qpm 以上；
	4.  etcd 写流量压力峰值在 20 万 pqm 以上，其中写 event 在 15k qpm 以上；
	5.  etcd CPU 经常性飙升到 900% 以上；
	6.  etcd 内存 RSS 在 60 GiB 以上；
	7.  etcd 磁盘使用量可达 100 GiB 以上；
	8.  etcd 自身的 goroutine 数量 9k 以上；
	9.  etcd 使用的用户态线程达 1.6k 以上；
	10.  etcd gc 单次耗时常态下可达 15ms。
	
	使用 Go 语言实现的 etcd 管理这些数据非常吃力，无论是 CPU、内存、gc、goroutine 数量还是线程使用量，基本上都接近 go runtime 管理能力极限：经常在 CPU profile 中观测到 go runtime 和 gc 占用资源超过 50% 以上。

	蚂蚁的 K8s 集群在经历高可用项目维护之前，当集群规模突破 7 千节点规模时，曾出现如下性能瓶颈问题：
	
	1.  etcd 出现大量的读写延迟，延迟甚至可达分钟级；
	2.  kube-apiserver 查询 pods / nodes / configmap / crd 延时很高，导致 etcd oom；
	3.  etcd list-all pods 时长可达 30 分钟以上；
	4.  2020 年 etcd 集群曾因 list-all 压力被打垮导致的事故就达好几起；
	5.  控制器无法及时感知数据变化，如出现 watch 数据延迟可达 30s 以上。