---
title: "使用 ssh-copy-id 工具自动上传公钥实现 ssh 连接不输入密码"
date: "2022-12-16"
---
一键，非常轻松
```bash
❯ ssh-copy-id root@10.131.200.144  
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/abser/.ssh/id_rsa.pub"  
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already  
installed  
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install  
the new keys  
root@10.131.200.144's password:    
Permission denied, please try again.  
root@10.131.200.144's password:    
  
Number of key(s) added: 1  
  
Now try logging into the machine, with:   "ssh 'root@10.131.200.144'"  
and check to make sure that only the key(s) you wanted were added.
```