---
title: "LVM 提示信息问题"
date: "2022-11-02"
aliases: []
---
## 摘要
`export LVM_SUPPRESS_FD_WARNINGS=1` 解决

## 主要笔记
- 每次执行 [[lvm]] 相关命令时都会弹出大量的报错信息，包含文件描述符泄露的信息：File descriptor 20 leaked on lvs invocation. Parent PID 1239361
-  https://unix.stackexchange.com/questions/4931/leaking-file-descriptors