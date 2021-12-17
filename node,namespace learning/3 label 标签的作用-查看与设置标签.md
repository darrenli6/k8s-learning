k8s集群如果由大量节点组成，可将节点打上对应的标签，然后通过标
签进行筛选，查看

查看节点标签信息


```
[root@master1 ~]# kubectl get node --show-labels
NAME STATUS ROLES AGE
VERSION LABELS
192.168.122.11 Ready,SchedulingDisabled master 5d1h
v1.18.3
beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,
kubernetes.io/arch=amd64,kubernetes.io/hostname=192.168.12
2.11,kubernetes.io/os=linux,kubernetes.io/role=master
192.168.122.12 Ready,SchedulingDisabled master 5d1h
v1.18.3
beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,
kubernetes.io/arch=amd64,kubernetes.io/hostname=192.168.12
2.12,kubernetes.io/os=linux,kubernetes.io/role=master
192.168.122.13 Ready node 5d1h
v1.18.3
beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,
kubernetes.io/arch=amd64,kubernetes.io/hostname=192.168.12
2.13,kubernetes.io/os=linux,kubernetes.io/role=node
192.168.122.14 Ready node 5d1h
v1.18.3
beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,
kubernetes.io/arch=amd64,kubernetes.io/hostname=192.168.12
2.14,kubernetes.io/os=linux,kubernetes.io/role=node
```

设置节点标签信息
为节点 192.168.122.13 打一个 region=huanai 的标签


```
[root@master1 ~]# kubectl label node 192.168.122.13
region=huanai
node/192.168.122.13 labeled
```


查看所有节点带region的标签


```
[root@master1 ~]# kubectl get nodes -L regio
```

