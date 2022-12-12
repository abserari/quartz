---
title: "KDE 窗口黑边，窗口管理器 Kwin 需要重启"
date: "2022-12-12"
aliases: []
---
## 摘要
输入命令可以通过 `alt+f2` 或者 `alt+space` 快捷键唤出快捷烂直接输入运行。
窗口卡死，没法切换了，通过命令
`kwin_x11 --place` 重启 kwin 解决
如果是 plasmashell 问题可以用 
`kquitapp5 plasmashell && kstart5 plasmashell`
或者 `plasmashell --place` 。

如果还没法解决，直接重启整个图形界面吧。
`sudo systemctl restart display-manager`

