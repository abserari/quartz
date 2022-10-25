---
title: "WOR"
date: "2022-10-25"
aliases: [Write-Once Register]
---
## 摘要
[[Paxos]] 是一种实现共识的协议。这意味着它实现了一个称为一次写入寄存器（**[[WOR|Write-Once Register]]**）（或 [[WOR]]）的逻辑对象。WOR 有一个简单的 API：您可以写入一次；你可以从中多次阅读。

```java
class WOR{
	public:
		//success means some write succeeded;
		//read after a write to see what was written.
		void write(std::string payload);
		//throw an exception if unwritten
		std::string read();
}
```
推导过程：
[[paxos 抽象理解]]

最终的 WOR API：

```java
class WOR{
	public:
		//lock a quorum
		int lock();
		//success means some write succeeded;
		//read after a write to see what was written.
		//throws an exception if you lost the lock.
		void write(std::string payload, int lockId);
		//throw an exception if unwritten
		std::string read();
}
```


## 问题、提示
-  

## 主要笔记
-  From [[paxos 抽象理解]]
- 一个 WOR 的实现必须表现得像一个很大的胖锁（不管它实际上是如何实现的）。

