[toc]


### yaml 写法


```
apiVersion: v1
kind: Namespace
metadata:
  name: ns1
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-stress2
  namespace: ns1
spec:
  containers:
  - name: c1
    image: polinux/stress
    imagePullPolicy: IfNotPresent
    resources:
      limits:
        memory: "200Mi"
      requests:
        memory: "100Mi"
    command: ["stress"] #启动容器时执行的命令
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang","1"] 
# 产生1个进程分配150M内存1秒后释放
```

执行结果

```
[root@slave1 centos]# kubectl get ns
NAME          STATUS   AGE
ca-sw         Active   12d
default       Active   12d
kube-public   Active   12d
kube-system   Active   12d
ns1           Active   12d
[root@slave1 centos]# kubectl get pod -n ns1
NAME                READY   STATUS    RESTARTS   AGE
orderer-0-myorg-0   1/1     Running   0          12d
orderer-1-myorg-0   1/1     Running   0          12d
orderer-2-myorg-0   1/1     Running   0          12d
peer-0-myorg-0      2/2     Running   0          12d
pod-stress2         1/1     Running   0          36s

```


与pod进行交互 


```
[root@slave1 centos]# kubectl exec -it pod-stress2 -c c1 -n ns1  /bin/bash
bash-5.0# str
bash: str: command not found

```

Mi 1024
M 1000 



我可以直接删除namespace 删除pod


```
[root@slave1 centos]# kubectl delete ns ns2
namespace "ns2" deleted

```


当--vm-bytes 修改为250M


```
[root@slave1 centos]# kubectl apply -f pod1.yaml
namespace/ns2 created
pod/pod-stress22 created
[root@slave1 centos]# kubectl get pods -n ns2
NAME           READY   STATUS             RESTARTS   AGE
pod-stress22   0/1     CrashLoopBackOff   1          7s

```
