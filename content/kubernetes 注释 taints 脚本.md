单节点 [[Kubernetes]] 需要注释一下 [[NoSchedule]] 的污点（[[taints]]），让 [[Pod]] 可以调度到节点上。
查询方式：
```bash
kubectl get no -o yaml | grep taint -A 5
```
删除方式
```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

[总所周知的标签、注解和污点](https://kubernetes.io/zh-cn/docs/reference/labels-annotations-taints/)