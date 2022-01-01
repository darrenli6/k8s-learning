

nodeslector  指定调度 


secret : 存放密码 如hub有密码，不用输入密码


configMap： 存放配置文件，比如nginx配置文件 


```
[root@slave2 home]# cat pod-nginx.conf
apiVersion: v1
kind: Pod
metadata:
  name: nginx2

spec:
  containers:
  - name: c1
    image: nginx:1.15-alpine

  - name: c2
    image: nginx:1.15-alpine

```


启动 


```
[root@slave2 home]# kubectl apply -f pod-nginx.yaml
pod/nginx2 created
 
[root@slave2 home]# kubectl get pods
NAME     READY   STATUS              RESTARTS   AGE
nginx2   0/2     ContainerCreating   0          15s
[root@slave2 home]# kubectl get pods
NAME     READY   STATUS              RESTARTS   AGE
nginx2   0/2     ContainerCreating   0          24s
[root@slave2 home]# kubectl describe nginx2
error: the server doesn't have a resource type "ngin



[root@slave2 home]# kubectl delete -f pod-nginx.yaml
pod "nginx2" deleted
[root@slave2 home]# kubectl get pods
No resources found.




```
