---
title: "LVM 重启需要重新初始化否则丢失 vg 设备的问题"
---
https://www.aboutyun.com/thread-16065-1-1.html

设置  开机挂载 ，ok了  
  
cat /etc/rc.d/rc.local |grep cinder-volumes || echo 'losetup -f /var/lib/cinder/cinder-volumes && vgchange -a y cinder-volumes ' >> /etc/rc.d/rc.local
