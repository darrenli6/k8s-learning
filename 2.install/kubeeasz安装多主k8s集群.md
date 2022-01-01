[toc]

# kubeeasz安装多主k8s集群



## 环境准备

### 机构说明

![image](https://note.youdao.com/yws/res/56420/749E8936E8074FE3AC8B01B0B968DF2E)



ip | 主机名 | 角色
---|--- | ---
192.168.10.11 | master1 | etcd master1 ansible 的管理节点
192.168.10.12 |  master2 | etcd master2 
192.168.10.13 | node1 |  etcd node1
192.168.10.14 | node2 |  node2


### 主机名配置与绑定


```
[root@localhost darren]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6


192.168.10.11 master1
192.168.10.12 master2
192.168.10.13 node1
192.168.10.14 node2

```



### 关闭 selinux 


```
[roo
t@localhost darren]# vim /etc/selinux/config




SELINUX=disabled

```


### 关闭防火墙,及其规则清理 重启



```
[root@localhost darren]# systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.


[root@master1 darren]# iptables -F
[root@master1 darren]# iptables -t nat
 
[root@master1 darren]# iptables -t nat -F
[root@master1 darren]# iptables -t mangle -F
[root@master1 darren]# iptables -t raw -F


reboot

```


## 软件包安装

在master节点安装 


```
[root@master1 darren]# yum install git python3-pip -y

```

```
pip install setuptools-rust
 pip3 install -U pip
 
 

[root@master1 ~]# pip3 install ansible==2.6.18
netaddr==0.7.19 -i https://mirrors.aliyun.com/pypi/simple/
```

安装成功

```
...
Using legacy 'setup.py install' for ansible, since package 'wheel' is not installed.
Installing collected packages: six, pynacl, cryptography, bcrypt, paramiko, netaddr, ansible
    Running setup.py install for ansible ... done
Successfully installed ansible-2.6.18 bcrypt-3.2.0 cryptography-36.0.1 netaddr-0.7.19 paramiko-2.8.1 pynacl-1.4.0 six-1.16.0

```


### ssh免密


```
[root@master1 ~]# ssh-keygen

[root@master1 ~]# ssh-copy-id master1
[root@master1 ~]# ssh-copy-id master2
[root@master1 ~]# ssh-copy-id node1
[root@master1 ~]# ssh-copy-id node2


```

### 下载安装工具easzup

```
[root@master1 ~]# curl -C- -fLO --retry 3
https://github.com/easzlab/kubeasz/releases/download/2.2.1/easzup

# 如果下载不了 可以本地下载上传即可 

```
### 安装并配置
```

[root@master1 ~]# chmod +x easzup
[root@master1 ~]# ll -h easzup
-rwxr-xr-x 1 root root 13K Aug 6 23:36 easzup

docker.io/mirrorgooglecontainers/metrics-server-amd64:v0.3.6
3.2: Pulling from easzlab/pause-amd64
c74f8866df09: Pull complete
Digest: sha256:4a1c4b21597c1b4415bdbecb28a3296c6b5e23ca4f9feeb599860a1dac6a0108
Status: Downloaded newer image for easzlab/pause-amd64:3.2
docker.io/easzlab/pause-amd64:3.2
2.2.1: Pulling from easzlab/kubeasz
Digest: sha256:c47613c6dd536ba14541e9e658e1e4a015f9c2e5ad7bab93c9c434153ad9bacb
Status: Image is up to date for easzlab/kubeasz:2.2.1
docker.io/easzlab/kubeasz:2.2.1
[INFO] Action successed : download_all



[root@master1 darren]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@master1 darren]# docker images
REPOSITORY                                    TAG                 IMAGE ID            CREATED             SIZE
easzlab/kubeasz                               2.2.1               a6e8894dba90        18 months ago       142MB
easzlab/kubeasz-ext-bin                       0.5.2               c27bc20945ad        19 months ago       178MB
kubernetesui/dashboard                        v2.0.1              85d666cddd04        19 months ago       223MB
easzlab/kubeasz-k8s-bin                       v1.18.3             eb70439de7b3        19 months ago       475MB
calico/node                                   v3.8.8-1            3610c051aa19        20 months ago       192MB
calico/cni                                    v3.8.8-1            ca2a236d9210        20 months ago       161MB
kubernetesui/metrics-scraper                  v1.0.4              86262685d9ab        20 months ago       36.9MB
calico/kube-controllers                       v3.8.8              7125b7d47e9f        21 months ago       48.9MB
calico/pod2daemon-flexvol                     v3.8.8              cacd6d732f12        21 months ago       9.38MB
easzlab/flannel                               v0.12.0-amd64       4e9f801d2217        21 months ago       52.8MB
easzlab/pause-amd64                           3.2                 80d28bedfe5d        22 months ago       683kB
coredns/coredns                               1.6.7               67da37a9a360        22 months ago       43.8MB
mirrorgooglecontainers/metrics-server-amd64   v0.3.6              9dd718864ce6        2 years ago         39.9MB




```


拷贝ansible清单文件


```
[root@master1 ~]# cd /etc/ansible && cp
example/hosts.multi-node hosts
```


配置结果如下


```
[root@master1 ansible]# egrep -v '^#|^$' hosts
[etcd]
192.168.10.11 NODE_NAME=etcd1
192.168.10.12 NODE_NAME=etcd2
192.168.10.13 NODE_NAME=etcd3 # ectd集群三台上(不
要用主机名，用IP，否则会ectd服务启动不了，如下图所示)
[kube-master]
192.168.10.11
192.168.10.12 # k8s主节点的IP，不
要用主机名，否则kube-api启动不了

[kube-node]
192.168.10.13
192.168.10.14
[harbor]
[ex-lb]
[chrony]
[all:vars]
CONTAINER_RUNTIME="docker"
CLUSTER_NETWORK="calico" # 网络方案建议使用calico
PROXY_MODE="ipvs"
SERVICE_CIDR="10.2.0.0/16"
CLUSTER_CIDR="10.3.0.0/16" # 服务网络与pod网络可不
改，我这里为了方便就改了一下（不要和集群节点网络一致)
NODE_PORT_RANGE="20000-40000"
CLUSTER_DNS_DOMAIN="cluster.local."
bin_dir="/opt/kube/bin"
ca_dir="/etc/kubernetes/ssl"
base_dir="/etc/ansible"


```


测试连通性 


```
[root@master1 ansible]# ansible all -m ping
192.168.10.13 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.10.12 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.10.14 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.10.11 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

```

## 开始集群安装
依次安装 

```
[root@master1 ansible]# ansible-playbook 01.prepare.yml
[root@master1 ansible]# ansible-playbook 02.etcd.yml
[root@master1 ansible]# ansible-playbook 03.docker.yml
[root@master1 ansible]# ansible-playbook 04.kubemaster.yml
[root@master1 ansible]# ansible-playbook 05.kube-node.yml
[root@master1 ansible]# ansible-playbook 06.network.yml
[root@master1 ansible]# ansible-playbook 07.clusteraddon.yml
```

### 安装后验证


```
[root@master1 ansible]# kubectl version
Client Version: version.Info{Major:"1", Minor:"18",
GitVersion:"v1.18.3",
GitCommit:"2e7996e3e2712684bc73f0dec0200d64eec7fe40",
GitTreeState:"clean", BuildDate:"2020-05-20T12:52:00Z",
GoVersion:"go1.13.9", Compiler:"gc",
Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"18",
GitVersion:"v1.18.3",
GitCommit:"2e7996e3e2712684bc73f0dec0200d64eec7fe40",
GitTreeState:"clean", BuildDate:"2020-05-20T12:43:34Z",
GoVersion:"go1.13.9", Compiler:"gc",
Platform:"linux/amd64"}
```



```
[root@master1 ansible]# kubectl get componentstatus
NAME STATUS MESSAGE ERROR
controller-manager Healthy ok
scheduler Healthy ok
etcd-2 Healthy {"health":"true"}
etcd-0 Healthy {"health":"true"}
etcd-1 Healthy {"health":"true"}
```



```
 [root@master1 ansible]# kubectl get nodes
NAME            STATUS                     ROLES    AGE     VERSION
192.168.10.11   Ready,SchedulingDisabled   master   4m20s   v1.18.3
192.168.10.12   Ready,SchedulingDisabled   master   4m21s   v1.18.3
192.168.10.13   Ready                      node     3m5s    v1.18.3
192.168.10.14   Ready                      node     3m6s    v1.18.3

```



```
[root@master1 ansible]# kubectl get pod --all-namespaces
NAMESPACE NAME
READY STATUS RESTARTS AGE
kube-system coredns-65dbdb44db-zmsrj
1/1 Running 0 7m26s
kube-system dashboard-metrics-scraper-545bbb8767-rvqjl
1/1 Running 0 6m13s
kube-system calico-kube-controllers-7fdc86d8ff-vb25c
1/1 Running 0 9m12s
kube-system calico-node-8mzvl
1/1 Running 0 9m12s
kube-system calico-node-ms27x
1/1 Running 0 9m12s
kube-system calico-node-v4cbs
1/1 Running 0 9m12s
kube-system calico-node-xbq8p
1/1 Running 0 9m12s
kube-system kubernetes-dashboard-65665f84db-7gnvn
1/1 Running 0 6m13s
kube-system metrics-server-869ffc99cd-qws8w
1/1 Running 0 7m12s
```



```
[root@master1 ansible]# kubectl get svc --all-namespaces
NAMESPACE NAME TYPE
CLUSTER-IP EXTERNAL-IP PORT(S) AGE
default kubernetes ClusterIP
10.68.0.1 <none> 443/TCP 14m
kube-system dashboard-metrics-scraper ClusterIP
10.68.25.239 <none> 8000/TCP
6m42s
kube-system kube-dns ClusterIP
10.68.0.2 <none> 53/UDP,53/TCP,9153/TCP
7m50s
kube-system kubernetes-dashboard NodePort
10.68.242.33 <none> 443:24949/TCP
6m45s
kube-system metrics-server ClusterIP
10.68.143.40 <none> 443/TCP
7m36s
```

## dashboard登录

### 查看 dashboard访问端口

```
[root@master1 ansible]# kubectl get svc -n kube-system
NAME                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
dashboard-metrics-scraper   ClusterIP   10.2.24.34    <none>        8000/TCP                 4m8s
kube-dns                    ClusterIP   10.2.0.2      <none>        53/UDP,53/TCP,9153/TCP   4m41s
kubernetes-dashboard        NodePort    10.2.252.14   <none>        443:29169/TCP            4m8s
metrics-server              ClusterIP   10.2.152.11   <none>        443/TCP                  4m33s

```

### 访问 


通过浏览器访问(IP为master的IP,端口为上面查询的端口,https协议)

![image](https://note.youdao.com/yws/res/56365/ABBA1A63989E46DEB8A1E6E0AFF012C7)



### 需要得到token


```
[root@master1 ansible]# kubectl -n kube-system describe secret admin-user
Name:         admin-user-token-9j9w8
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 060a495d-ff57-48a6-8770-f31e6de67e82

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1350 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IkRVUXVIeGwzczROdVFLcXBaTkZBUDNjdXlzWEZqNElKRnFEbEpWa2RsRkkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLTlqOXc4Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIwNjBhNDk1ZC1mZjU3LTQ4YTYtODc3MC1mMzFlNmRlNjdlODIiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.W2Tw_reZ6tRJjExKq-b2JWOTiDfKnZUswHPCJ0lCmmrUSBs2vr_B_JCMw2t5KQi_Ln-YMB7C8kWNLYHKWPIEgSJvJxKMn8hcKFI4MtrZ_K11PCT6KmSXeskZ6dAOaR1w7hpwRHdwpmqMkWi_rTePsmVfrK85zkXRitIctKkjbwzdOUgI69VPxKnzWJAZYT_Js7hdzr7v_CqKoufnZOel0BbB9Yzsah_7RABtvfcSWDw8JajnNLzhcY5WFT-waQqd1rMSfBHSpkbO4btn0cXGutM-u_qTGqZoeZ5H3Y-EQX1YMvaVOeY8APmt5yWN7I_NJxagvDf_1EGwfmqIKgJUUA

[root@master1 ansible]#

```


复制token登录


### 登录

![image](https://note.youdao.com/yws/res/56370/A49C079148BD41729487B164C5DF339E)



## rancher


### 安装

我们在node2节点安装

```
[root@node2 darren]# docker run -d --restart=unless-stopped -p 80:80 -p 443:443 --name rancher -v /var/lib/rancher/:/var/lib/rancher/ --privileged rancher/rancher:stable
Unable to find image 'rancher/rancher:stable' locally
stable: Pulling from rancher/rancher
af637d9f9908: Pull complete
f9224574abce: Pull complete
ebae3352c76e: Pull complete
7b21d0842057: Pull complete
88a4b8d1c9ea: Pull complete
068e40008a4a: Pull complete
af8d478ed359: Pull complete
0570bed5c15a: Pull complete
f7dfd304c929: Pull complete
e0f5d341d113: Pull complete
bd89a58c597d: Pull complete
c03c718eef46: Pull complete
5e1f456cdbf8: Pull complete
e55dca76e75f: Pull complete
c9a821480bc3: Pull complete
c6e3491b680d: Pull complete
9d7c10a11f0f: Pull complete
Digest: sha256:51658639bf2b28f4796e7daa04b0434e29fcf69ce062bf649462be77ae76cd2c
Status: Downloaded newer image for rancher/rancher:stable
775fae8dbcf7ec562d25aaadf4ed58a7ce99086ba465b47f652609aa7075c9cc
[root@node2 darren]# docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                                      NAMES
775fae8dbcf7        rancher/rancher:stable    "entrypoint.sh"          2 minutes ago       Up 2 minutes        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   rancher
51b8447dc412        86262685d9ab              "/metrics-sidecar"       About an hour ago   Up About an hour                                               k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-545bbb8767-pdw2q_kube-system_d0778a13-0d75-4fb2-98e1-e7001f448d55_0
03ff672fb7e9        easzlab/pause-amd64:3.2   "/pause"                 About an hour ago   Up About an hour                                               k8s_POD_dashboard-metrics-scraper-545bbb8767-pdw2q_kube-system_d0778a13-0d75-4fb2-98e1-e7001f448d55_0
c7e3fadf4a44        9dd718864ce6              "/metrics-server --c…"   About an hour ago   Up About an hour                                               k8s_metrics-server_metrics-server-869ffc99cd-fffcg_kube-system_b463d3a0-246a-4ef3-8e5c-d74cc2fe8407_0
440c2a177e1b        easzlab/pause-amd64:3.2   "/pause"                 About an hour ago   Up About an hour                                               k8s_POD_metrics-server-869ffc99cd-fffcg_kube-system_b463d3a0-246a-4ef3-8e5c-d74cc2fe8407_0
5211bd7b3232        67da37a9a360              "/coredns -conf /etc…"   About an hour ago   Up About an hour                                               k8s_coredns_coredns-65dbdb44db-w46tb_kube-system_8c41067c-bf89-4249-a4dd-20e00dad8a3b_0
0e5365655f3a        easzlab/pause-amd64:3.2   "/pause"                 About an hour ago   Up About an hour                                               k8s_POD_coredns-65dbdb44db-w46tb_kube-system_8c41067c-bf89-4249-a4dd-20e00dad8a3b_0
640ecf84f80f        3610c051aa19              "start_runit"            About an hour ago   Up About an hour                                               k8s_calico-node_calico-node-p2zj9_kube-system_a8c8d36c-ee4e-4ea4-8496-19eb07623f44_0
396aea7f12e3        easzlab/pause-amd64:3.2   "/pause"                 About an hour ago   Up About an hour                                               k8s_POD_calico-node-p2zj9_kube-system_a8c8d36c-ee4e-4ea4-8496-19eb07623f44_0

```

### 访问 

https://192.168.10.14


![image](https://note.youdao.com/yws/res/56387/86A5341998C44A9B8BE10F16ABD7FCF1)

查看登录密码


```
[root@node2 darren]# docker logs  775f  2>&1 | grep "Bootstrap Password:"
2021/12/16 08:40:33 [INFO] Bootstrap Password: 2ds8m2cms2mgslljz4gzx8kq4v725dnz6sbcjls5bjw872tpp54bt7
[root@node2 darren]#

```

设置密码


![image](https://note.youdao.com/yws/res/56391/5DBBCDA44C014F9D8C081574CAC9894C)


![image](https://note.youdao.com/yws/res/56393/880204D02C754F2EBB8777A05CBC9CC1)

导入集群 

![image](https://note.youdao.com/yws/res/56395/4E757A7881D74A0694DEE044A4ACA41D)

![image](https://note.youdao.com/yws/res/56397/853805B13A654E399FAD36AABF891945)

![image](https://note.youdao.com/yws/res/56399/494D3A0035BA47E5816F847CA2C68A05)


因为是自签ca

![image](https://note.youdao.com/yws/res/56401/E4E8EB157B7545ED83E91E2BA5BA247C)


```
curl --insecure -sfL https://192.168.10.14/v3/import/m9xfbt7d8c84hvjbddh6l29h5dcvjxrn8h7chb6nzs25tzdnfcxk6r_c-m-l88fgnd6.yaml | kubectl apply -f -
```

复制上述命令在master1节点执行 

进入pending状态 


![image](https://note.youdao.com/yws/res/56411/D6EDCA7668EB4216A68D73BAB881CF6C)




### 验证导入结果

![image](https://note.youdao.com/yws/res/56413/78400DF8E5FF4AE694887F5C111F7181)


### 查看rancher的资源



```
[root@master1 darren]# kubectl get ns
NAME                          STATUS   AGE
cattle-impersonation-system   Active   3m48s
cattle-system                 Active   6m11s
default                       Active   85m
kube-node-lease               Active   85m
kube-public                   Active   85m
kube-system                   Active   85m

```


```
[root@master1 darren]# kubectl get all -n cattle-system
NAME                                        READY   STATUS             RESTARTS   AGE
pod/cattle-cluster-agent-5c456cf89c-fs6c9   0/1     CrashLoopBackOff   3          3m18s

NAME                           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/cattle-cluster-agent   ClusterIP   10.2.172.166   <none>        80/TCP,443/TCP   7m23s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/cattle-cluster-agent   0/1     1            0           7m23s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/cattle-cluster-agent-5c456cf89c   1         1         0       3m18s
replicaset.apps/cattle-cluster-agent-869ff57f98   0         0         0       7m23s

```

