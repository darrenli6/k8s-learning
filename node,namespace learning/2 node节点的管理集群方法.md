查看节点信息


```
[root@master1 ~]# kubectl get nodes
NAME STATUS ROLES AGE
VERSION
192.168.122.11 Ready,SchedulingDisabled master 1h
v1.18.3
192.168.122.12 Ready,SchedulingDisabled master 1h
v1.18.3
192.168.122.13 Ready node 1h
v1.18.3
192.168.122.14 Ready node 1h
v1.18.3
```
查看节点详细信息

```
[root@master1 ~]# kubectl get nodes -o wide
```

描述节点详细信息

```
[root@master1 ~]# kubectl describe node 192.168.122.11
Name: 192.168.122.11
Roles: master
Labels: beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linuxkubernetes.io/arch=amd64
kubernetes.io/hostname=192.168.122.11
kubernetes.io/os=linux
kubernetes.io/role=master
Annotations: node.alpha.kubernetes.io/ttl: 0
volumes.kubernetes.io/controllermanaged-attach-detach: true
CreationTimestamp: Tue, 27 Oct 2020 22:14:11 +0800
Taints:
node.kubernetes.io/unschedulable:NoSchedule
Unschedulable: true
Lease:
HolderIdentity: 192.168.122.11
AcquireTime: <unset>
RenewTime: Sun, 01 Nov 2020 23:18:17 +0800
Conditions:
Type Status LastHeartbeatTime
LastTransitionTime Reason
Message
---- ------ -----------------
------------------ ------
-------
MemoryPressure False Sun, 01 Nov 2020 23:18:18 +0800
Tue, 27 Oct 2020 22:14:10 +0800
KubeletHasSufficientMemory kubelet has sufficient memory
available
DiskPressure False Sun, 01 Nov 2020 23:18:18 +0800
Tue, 27 Oct 2020 22:14:10 +0800
KubeletHasNoDiskPressure kubelet has no disk pressure
PIDPressure False Sun, 01 Nov 2020 23:18:18 +0800
Tue, 27 Oct 2020 22:14:10 +0800
KubeletHasSufficientPID kubelet has sufficient PID
available
Ready True Sun, 01 Nov 2020 23:18:18 +0800
Sun, 01 Nov 2020 22:39:43 +0800 KubeletReady
kubelet is posting ready status
Addresses:
InternalIP: 192.168.122.11
Hostname: 192.168.122.11
Capacity:
cpu: 8
```

