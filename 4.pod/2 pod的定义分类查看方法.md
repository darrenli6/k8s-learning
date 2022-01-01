### pod的定义分类 

参考: https://kubernetes.io/zh/docs/concepts/workloads/pods/



- Pod(豆荚) 是Kubernetes集群管理与调度的最小单元
- 一个Pod可以封装一个容器或多个容器(主容器或sidecar边车容器)
- 一个pod内的多个容器之间共享部分命名空间，例如：Net
Namespace,UTS Namespace,IPC Namespace及存储资源、
- 用户pod默认会被调度运行在node节点之上(不运行在master节点上，
但也有例外情况)
pod内的IP不是固定的，集群外不能直接访问pod

一个pod只有一个ip



### pod可分为:
无控制器管理的自主式pod 没有副本控制器控制，删除自主式pod后
不会重新创建
控制器管理的pod 控制器可以控制pod的副本数，扩容与裁剪，版本
更新与回滚等

### 查看pod方法
pod是一种资源，可以通过 kubectl get pod 来查看


```
[root@master1 ~]# kubectl get pod # pod或pods都可以，
不指定namespace,默认是名为default的namespace
[root@master1 ~]# kubectl get pod -n kube-system
```
