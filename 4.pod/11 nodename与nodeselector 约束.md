[toc]

pod-nodename.yaml


```
apiVersion: v1
kind: Pod
metadata:
  name: pod-nodename
spec:
  containers:
  - name: c1
    image: nginx:1.15-alpine
  nodeName: 192.168.10.244
```


pod-nodeselector.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-nodeselector
spec:
  containers:
  - name: c1
    image: nginx:1.15-alpine
  nodeSelector:
    region: sz
    
```


nodeName 用于将pod调度到制定的node名称上

nodeselector 用于将pod调度到匹配label的node上

