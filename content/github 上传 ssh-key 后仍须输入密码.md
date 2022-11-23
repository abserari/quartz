---
title: "github 上传 ssh-key 后仍须输入密码"
date: "2022-11-23"
aliases: []
---
## 摘要
`.git/config` 文件写明了通过 http 协议，没有走 ssh
`url = https://github.com/Name/project.git`
改为
`url = git@github.com:Name/project.git`

只想走 ssh 协议。from：[Git - git-config Documentation](https://git-scm.com/docs/git-config#Documentation/git-config.txt-urlltbasegtinsteadOf)
```bash
git config --global url.ssh://git@github.com/.insteadOf https://github.com/
```

#### 测试连通性
```bash
ssh -T git@github.com
```
