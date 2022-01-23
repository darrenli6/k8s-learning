[toc]

#### 查看pod
```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl describe pod nginx-deployment-66b6c48dd5-4h29v
Name:         nginx-deployment-66b6c48dd5-4h29v
Namespace:    default
Priority:     0
Node:         docker-desktop/192.168.65.4
Start Time:   Sat, 22 Jan 2022 23:13:51 +0800
Labels:       app=nginx
              pod-template-hash=66b6c48dd5
Annotations:  <none>
Status:       Running
IP:           10.1.0.9
IPs:
  IP:           10.1.0.9
Controlled By:  ReplicaSet/nginx-deployment-66b6c48dd5
Containers:
  nginx:
    Container ID:   docker://a8fd3f8fa1e836d59dd224b250054a4c5c7729c1f679b8b96d1a175838c504cb
    Image:          nginx:1.14.2
    Image ID:       docker-pullable://nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 22 Jan 2022 23:29:24 +0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-cxfl2 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-cxfl2:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-cxfl2
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:          <none>
(base) DarrenlideMacBook-Pro:k8s darren$ 

```


2, 升级为1.16版 


```
[root@master1 ~]# kubectl set image deployment deploy- nginx nginx=nginx:1.16-alpine --record deployment.apps/deploy-nginx image updated
```

说明:deployment deploy-nginx代表名为deploy-nginx的deployment

nginx=nginx:1.16-alpine 前面的nginx为容器名

--record 表示会记录


效果


```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl set image deployment nginx-deployment nginx=nginx:1.16-alpine --record
deployment.apps/nginx-deployment image updated
```

注意 nginx是容器名



```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-6cb4d85b97-56hzh   1/1     Running   0          2m52s
nginx-deployment-6cb4d85b97-5fksq   1/1     Running   0          2m48s
```

升级之后的name变化了


 

- 查看版本历史

```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl rollout history deployment nginx-deployment 
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deployment nginx-deployment nginx=nginx:1.16-alpine --record=true
```

 

- 执行回退版本


```
kubectl rollout undo   deployment nginx-deployment --to-revision=1
```

- 查看效果


```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-66b6c48dd5-qb5t4   0/1     ContainerCreating   0          3s
nginx-deployment-6cb4d85b97-56hzh   1/1     Running             0          10m
nginx-deployment-6cb4d85b97-5fksq   1/1     Running             0          10m
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-66b6c48dd5-qb5t4   1/1     Running       0          10s
nginx-deployment-66b6c48dd5-spbl7   1/1     Running       0          6s
nginx-deployment-6cb4d85b97-56hzh   1/1     Terminating   0          10m
nginx-deployment-6cb4d85b97-5fksq   0/1     Terminating   0          10m
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-66b6c48dd5-qb5t4   1/1     Running       0          11s
nginx-deployment-66b6c48dd5-spbl7   1/1     Running       0          7s
nginx-deployment-6cb4d85b97-56hzh   0/1     Terminating   0          10m
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-66b6c48dd5-qb5t4   1/1     Running   0          12s
nginx-deployment-66b6c48dd5-spbl7   1/1     Running   0          8s
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-66b6c48dd5-qb5t4   1/1     Running   0          13s
nginx-deployment-66b6c48dd5-spbl7   1/1     Running   0          9s

```


### 副本扩容  8个 


kubectl scale deployment nginx-deployment --replicas=8


```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP          NODE             NOMINATED NODE   READINESS GATES
nginx-deployment-66b6c48dd5-4j4bh   1/1     Running   0          35s   10.1.0.18   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-4rf2j   1/1     Running   0          35s   10.1.0.15   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-jlkmc   1/1     Running   0          35s   10.1.0.17   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-jptfr   1/1     Running   0          35s   10.1.0.16   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-n4cfx   1/1     Running   0          35s   10.1.0.19   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-qb5t4   1/1     Running   0          42m   10.1.0.12   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-spbl7   1/1     Running   0          42m   10.1.0.13   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-zvw57   1/1     Running   0          35s   10.1.0.14   docker-desktop   <none>           <none>
```


### 副本裁剪 




```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl scale deployment nginx-deployment --replicas=1
deployment.apps/nginx-deployment scaled
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods -o wide
NAME                                READY   STATUS        RESTARTS   AGE   IP          NODE             NOMINATED NODE   READINESS GATES
nginx-deployment-66b6c48dd5-4j4bh   1/1     Terminating   0          94s   10.1.0.18   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-4rf2j   1/1     Terminating   0          94s   10.1.0.15   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-jlkmc   1/1     Terminating   0          94s   10.1.0.17   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-jptfr   1/1     Terminating   0          94s   10.1.0.16   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-n4cfx   1/1     Terminating   0          94s   10.1.0.19   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-qb5t4   1/1     Running       0          43m   10.1.0.12   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-spbl7   1/1     Terminating   0          43m   10.1.0.13   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-zvw57   1/1     Terminating   0          94s   10.1.0.14   docker-desktop   <none>           <none>
```

### 删除

kubectl delete deployments.apps nginx-deployment


```
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods -o wide
NAME                                READY   STATUS        RESTARTS   AGE     IP          NODE             NOMINATED NODE   READINESS GATES
nginx-deployment-66b6c48dd5-lpq72   1/1     Terminating   0          3m27s   10.1.0.20   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-nqktn   1/1     Terminating   0          3m27s   10.1.0.22   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-qb5t4   1/1     Terminating   0          47m     10.1.0.12   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-rs27t   1/1     Terminating   0          3m27s   10.1.0.21   docker-desktop   <none>           <none>
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods -o wide
NAME                                READY   STATUS        RESTARTS   AGE     IP          NODE             NOMINATED NODE   READINESS GATES
nginx-deployment-66b6c48dd5-lpq72   1/1     Terminating   0          3m29s   10.1.0.20   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-nqktn   1/1     Terminating   0          3m29s   10.1.0.22   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-qb5t4   1/1     Terminating   0          47m     10.1.0.12   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-rs27t   1/1     Terminating   0          3m29s   10.1.0.21   docker-desktop   <none>           <none>
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods -o wide
NAME                                READY   STATUS        RESTARTS   AGE     IP          NODE             NOMINATED NODE   READINESS GATES
nginx-deployment-66b6c48dd5-lpq72   0/1     Terminating   0          3m36s   <none>      docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-nqktn   0/1     Terminating   0          3m36s   <none>      docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-qb5t4   0/1     Terminating   0          47m     10.1.0.12   docker-desktop   <none>           <none>
nginx-deployment-66b6c48dd5-rs27t   0/1     Terminating   0          3m36s   10.1.0.21   docker-desktop   <none>           <none>
(base) DarrenlideMacBook-Pro:k8s darren$ kubectl get pods -o wide
No resources found in default namespace.

```
