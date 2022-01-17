
```
[centos@slave1 ~]$ kubectl get deploy
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2         2         2            2           32h
[centos@slave1 ~]$ kubectl get pod
NAME                                READY   STATUS      RESTARTS   AGE
liveness-exec                       0/1     Completed   0          10d
liveness-httpget                    1/1     Running     1          5d1h
nginx-deployment-7bdbfcbc9c-s4hdw   1/1     Running     0          32h
nginx-deployment-7bdbfcbc9c-s9lvr   1/1     Running     0          32h
pod-nodeselector                    0/1     Pending     0          14d
[centos@slave1 ~]$ kubectl get pods -o wide
NAME                                READY   STATUS      RESTARTS   AGE    IP              NODE     NOMINATED NODE
liveness-exec                       0/1     Completed   0          10d    10.42.4.5       slave2   <none>
liveness-httpget                    1/1     Running     1          5d1h   10.42.38.35     slave2   <none>
nginx-deployment-7bdbfcbc9c-s4hdw   1/1     Running     0          32h    10.42.125.215   slave2   <none>
nginx-deployment-7bdbfcbc9c-s9lvr   1/1     Running     0          32h    10.42.12.128    slave1   <none>
pod-nodeselector                    0/1     Pending     0          14d    <none>          <none>   <none>
[centos@slave1 ~]$ curl 10.42.125.215
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
[centos@slave1 ~]$ curl 10.42.125.21

```


cluster_cidr= 10.42

我删除pod , 有控制器会促使重启


```
[centos@slave1 ~]$ kubectl delete pod nginx-deployment-7bdbfcbc9c-s4hdw
pod "nginx-deployment-7bdbfcbc9c-s4hdw" deleted
[centos@slave1 ~]$ kubectl get pod
NAME                                READY   STATUS      RESTARTS   AGE
liveness-exec                       0/1     Completed   0          10d
liveness-httpget                    1/1     Running     1          5d1h
nginx-deployment-7bdbfcbc9c-kqqfg   1/1     Running     0          8s
nginx-deployment-7bdbfcbc9c-s9lvr   1/1     Running     0          32h
pod-nodeselector                    0/1     Pending     0          14d
[centos@slave1 ~]$ kubectl get deploy
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2         2         2            2           32h

```


既然ip地址会变化，所以需要一个固定的ip访问  endpoint 给用户，那么那种方式就是service
