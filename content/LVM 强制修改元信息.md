Refer： https://listman.redhat.com/archives/linux-lvm/2012-October/msg00030.html

```bash
vgcfgbackup -f  vg.bak   vgname

edit vg.bak and remove all thinp related volumes

vgcfgrestore -f vg.bak  vgname
```
三步中，第二步要酌情删除目标项。