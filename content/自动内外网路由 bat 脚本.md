---
title: "自动内外网路由 bat 脚本"
date: "2022-10-20"
aliases: []
---
```batch
@:loop
@time /T
@REM REM 是注释. '@'是关闭回显.
@REM 1.1.1.1 是网关, 对应修改.
@REM For Chengdu
@set gateway=1.1.1.1
@REM For Shenzhen
@REM @set gateway=1.1.1.1

@REM 内网网络每间隔几秒钟会添加自己的网关为默认网关, 导致不能连通wifi外网
@REM 删除内网网络添加的默认网关后, wifi网络才能连通.
@route delete 0.0.0.0 %gateway% 2>nul

@REM 根据自己的访问需求, 添加内网网段的白名单, 走内网路由.
@route add 10.0.0.0  mask 255.0.0.0 %gateway% 2>nul
@route add 200.200.0.0 mask 255.255.0.0 %gateway% 2>nul
@route add 200.201.0.0 mask 255.255.0.0 %gateway% 2>nul
@route add 192.200.0.0 mask 255.255.0.0 %gateway% 2>nul

@timeout /T 1 /NOBREAK
@goto :loop
```
