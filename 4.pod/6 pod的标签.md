[toc]

### pod的标签 

- 为pod设置label 用于控制器通过label与pod关联


- 语法与前面学的node标签几乎一致 


查看pod的标签


```
[root@slave1 centos]# kubectl get pods --show-labels
No resources found.

```

打标签


kubeclt label pod pod-streaa1 app=stress 


根据条件查找 


```
kubectl get pods -l "app in (nginx,stress)"
```
删除标签


```
kubectl label pod nginx app-

```

删除pod


```
kubectl delete pod  xxx
```



