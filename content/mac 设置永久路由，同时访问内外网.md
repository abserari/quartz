[[2022-12-15]] 今天想起来以前设置了路由没有记笔记：

mac 添加永久路由的方法
1. 查看网卡名称列表 `networksetup -listallnetworkservices`
2. 设置路由：`networksetup -setadditionalroutes "AX1862" 172.11.0.0 255.255.255.0 172.16.198.1 192.160.0.0 255.255.255.0 172.16.198.1` 代表为指定网卡添加路由，网卡名称由第一条命令查询，依次是目的 IP，子关掩码，目标网关（通常是你的路由器的地址，可以从自己的 IPV4 连接状态中获取）。
3. 查看添加的路由 `networksetup -getadditionalroutes "AX1862"`
4. 清空路由表 `networksetup -setadditionalroutes "AX1862"`

常规方法就是使用 route 命令。