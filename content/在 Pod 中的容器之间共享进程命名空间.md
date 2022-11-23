[在 Pod 中的容器之间共享进程命名空间 | Kubernetes](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/#%E9%85%8D%E7%BD%AE-pod)

```bash
ps ax
```

输出类似于：

```bash
PID   USER     TIME  COMMAND
    1 root      0:00 /pause
    8 root      0:00 nginx: master process nginx -g daemon off;
   15 root      0:00 sh
   22 101       0:00 nginx: worker process
   23 root      0:00 ps ax
```