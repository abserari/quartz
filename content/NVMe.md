Non-Volatile Memory Host Controller Interface Specification，非易失性[[存储]]主机控制器接口标准

- 为什么 NVMe 更快 #query 
	- [[PCIe]]：总线带来更大带宽和更低延迟
	- 并行：传统的[[SATA]]设备只能支持一个队列，一次只能接收32条数据；而NVMe存储则支持最多64000个队列，每个队列有64000个条目。
	- [[SATA]] 协议理论最大传输速度 6.0Gbps