[toc]


```
kubectl get pods
```

删除pod 

```
[root@slave1 centos]# kubectl delete pod pod-stress1
pod "pod-stress1" deleted
[root@slave1 centos]# kubectl get pods
NAME          READY   STATUS             RESTARTS   AGE
pod-stress2   0/1     CrashLoopBackOff   2          37s

```
kubectl delete pod xxx1 xxx2



通过awk删除


```
[root@slave1 centos]# kubectl get pods  | awk 'NR > 1 {print $1}' | xargs  kubectl delete pod
pod "pod-stress1" deleted
pod "pod-stress2" deleted
pod "pod-stress3" deleted

```

