
设置标签 
```
[root@slave centos]# kubectl label nodes slave region=beijing zone=A env=dev
node/slave labeled

```

查看标签
```
[root@slave centos]# kubectl get nodes --show-labels
NAME     STATUS                     ROLES    AGE   VERSION            LABELS
master   Ready,SchedulingDisabled   <none>   18d   v1.12.7-rancher1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=rancher,beta.kubernetes.io/os=linux,etcd=true,failure-domain.beta.kubernetes.io/region=Region1,failure-domain.beta.kubernetes.io/zone=FailureDomain1,io.rancher.host.docker_version=18.06,io.rancher.host.kvm=true,io.rancher.host.linux_kernel_version=3.10,io.rancher.host.os=linux,kubernetes.io/hostname=master,orchestration=true,region=beijing
slave    Ready                      <none>   18d   v1.12.7-rancher1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=rancher,beta.kubernetes.io/os=linux,compute=true,env=dev,failure-domain.beta.kubernetes.io/region=Region1,failure-domain.beta.kubernetes.io/zone=FailureDomain1,io.rancher.host.docker_version=18.06,io.rancher.host.kvm=true,io.rancher.host.linux_kernel_version=3.10,io.rancher.host.os=linux,kubernetes.io/hostname=slave,localstorage=true,region=beijing,type=kube-node-registry,zone=A
slave2   Ready,SchedulingDisabled   <none>   16d   v1.12.7-rancher1   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=rancher,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=Region1,failure-domain.beta.kubernetes.io/zone=FailureDomain1,io.rancher.host.docker_version=18.06,io.rancher.host.kvm=true,io.rancher.host.lin
```
可以通过标签查看容器情况 

-L 或者 

-l 并且
```
[root@slave centos]# kubectl get nodes -L  region,zone,env
NAME     STATUS                     ROLES    AGE   VERSION            REGION    ZONE   ENV
master   Ready,SchedulingDisabled   <none>   18d   v1.12.7-rancher1   beijing
slave    Ready                      <none>   18d   v1.12.7-rancher1   beijing   A      dev
slave2   Ready,SchedulingDisabled   <none>   16d   v1.12.7-rancher1

```


覆盖修改 


```
[root@slave centos]# kubectl label nodes slave2 region=shanghai
error: 'region' already has a value (shanghai), and --overwrite is false
[root@slave centos]# kubectl label nodes slave2 region=shanghai --overwrite
node/slave2 not labeled


```


删除标签 


```

[root@slave centos]# kubectl label  nodes slave2  region- zone- 
node/slave2 labeled
 
[root@slave centos]# kubectl get nodes -L region,zone
NAME     STATUS                     ROLES    AGE   VERSION            REGION    ZONE
master   Ready,SchedulingDisabled   <none>   18d   v1.12.7-rancher1   beijing
slave    Ready                      <none>   18d   v1.12.7-rancher1   beijing   A
slave2   Ready,SchedulingDisabled   <none>   16d   v1.12.7-rancher1

```
