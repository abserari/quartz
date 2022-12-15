---
title: "git 配置多个提交用户自动匹配切换"
date: "2022-12-14"
aliases: []
---
## 摘要
from：[如何配置多个提交用户？ - Git 进阶指南](https://gb.yekai.net/questions/git-config-user)
Conditional Includes

在 git 2.13 版本中，增加了 [conditional includes](https://git-scm.com/docs/git-config#_includes) 配置，可以创建多个 gitconfig 文件，并针对不同的根目录使用不同的配置文件。例如，以下全局配置文件 `~/.gitconfig` 中包含以下用户配置信息，当项目 clone 在 `~/dev/` 目录下时，会自动使用另外一份配置文件：

```
[user]

name = Your Name

email = your_email@example.com

[includeIf "gitdir:~/dev/"]

path = .gitconfig-dev
```

以下是 `~/.gitconfig-dev` 文件的配置：
```
[user]

name = Another Name

email = another_email@example.com

```