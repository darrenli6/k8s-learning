
- Namespace是对一组资源和对象的抽象集合.（类似于mysql创建database ）
- 常见的 pod, service, deployment 等都是属于某一个namespace的
（默认是 default）
不是所有资源都属于namespace，如nodes, persistent volume，
-  namespace 等资源则不属于任何 namespace
-  

node 是物理上的划分 

namespace 是逻辑的划分 


### 查看namespace


```
[root@slave centos]# kubectl get namespace
NAME          STATUS   AGE
bass1         Active   18d
bass2         Active   16d
ca-baas1      Active   18d
default       Active   18d
kube-public   Active   18d
kube-system   Active   18d

```

### 查看namespace里的资源
使用 kubectl get all --namespace=命名空间名称 可以查看此命名空间
下的所有资源


```
[root@slave centos]# kubectl get all --namespace=bass1
NAME                  READY   STATUS    RESTARTS   AGE
pod/orderer-0-org-0   1/1     Running   0          18d
pod/orderer-1-org-0   1/1     Running   0          18d
pod/orderer-2-org-0   1/1     Running   0          18d
pod/peer-0-org-0      2/2     Running   0          17d

NAME                    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                                        AGE
service/orderer-0-org   NodePort   10.43.76.71     <none>        7050:30200/TCP,9443:30201/TCP                  18d
service/orderer-1-org   NodePort   10.43.130.169   <none>        7050:30300/TCP,9443:30301/TCP                  18d
service/orderer-2-org   NodePort   10.43.205.163   <none>        7050:30400/TCP,9443:30401/TCP                  18d
service/peer-0-org      NodePort   10.43.70.190    <none>        7051:30100/TCP,7052:30101/TCP,9443:30102/TCP   18d

NAME                             DESIRED   CURRENT   AGE
statefulset.apps/orderer-0-org   1         1         18d
statefulset.apps/orderer-1-org   1         1         18d
statefulset.apps/orderer-2-org   1         1         18d
statefulset.apps/peer-0-org      1         1         18d

```


```
[root@slave centos]# kubectl get all -n kube-system
NAME                                        READY   STATUS    RESTARTS   AGE
pod/default-http-backend-67f88b66c5-92pnz   1/1     Running   0          18d
pod/heapster-7bc7c7df4f-htwjx               1/1     Running   0          18d
pod/kube-dns-56495f67b9-5cn4n               3/3     Running   0          18d
pod/kube-registry-v0-5cn9t                  1/1     Running   0          18d
pod/kubernetes-dashboard-97db7f6d-9664b     1/1     Running   0          18d
pod/monitoring-grafana-74d844f69d-sqvp4     1/1     Running   0          18d
pod/monitoring-influxdb-d95b76bdf-tfd78     1/1     Running   0          18d
pod/nginx-ingress-rc-gpcns                  1/1     Running   0          18d
pod/tiller-deploy-64799799c-mxcjv           1/1     Running   0          18d

NAME                                     DESIRED   CURRENT   READY   AGE
replicationcontroller/kube-registry-v0   1         1         1       18d
replicationcontroller/nginx-ingress-rc   1         1         1       18d

NAME                           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
service/default-http-backend   ClusterIP   10.43.200.20    <none>        80/TCP          18d
service/heapster               ClusterIP   10.43.23.84     <none>        80/TCP          18d
service/kube-dns               ClusterIP   10.43.0.10      <none>        53/UDP,53/TCP   18d
service/kube-registry          ClusterIP   10.43.81.67     <none>        5000/TCP        18d
service/kubernetes-dashboard   ClusterIP   10.43.13.134    <none>        80/TCP          18d
service/monitoring-grafana     ClusterIP   10.43.157.253   <none>        80/TCP          18d
service/monitoring-influxdb    ClusterIP   10.43.162.245   <none>        8086/TCP        18d
service/tiller-deploy          ClusterIP   10.43.31.187    <none>        44134/TCP       18d

NAME                                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/default-http-backend   1         1         1            1           18d
deployment.apps/heapster               1         1         1            1           18d
deployment.apps/kube-dns               1         1         1            1           18d
deployment.apps/kubernetes-dashboard   1         1         1            1           18d
deployment.apps/monitoring-grafana     1         1         1            1           18d
deployment.apps/monitoring-influxdb    1         1         1            1           18d
deployment.apps/tiller-deploy          1         1         1            1           18d

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/default-http-backend-67f88b66c5   1         1         1       18d
replicaset.apps/heapster-7bc7c7df4f               1         1         1       18d
replicaset.apps/kube-dns-56495f67b9               1         1         1       18d
replicaset.apps/kubernetes-dashboard-97db7f6d     1         1         1       18d
replicaset.apps/monitoring-grafana-74d844f69d     1         1         1       18d
replicaset.apps/monitoring-influxdb-d95b76bdf     1         1         1       18d
replicaset.apps/tiller-deploy-64799799c           1         1         1       18d

```


