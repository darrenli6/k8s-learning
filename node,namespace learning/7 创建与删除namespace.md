

创建namespace 
```
[root@slave centos]# kubectl create ns ns1
namespace/ns1 created
[root@slave centos]# kubectl get ns
NAME          STATUS   AGE
bass1         Active   18d
bass2         Active   16d
ca-baas1      Active   18d
default       Active   18d
kube-public   Active   18d
kube-system   Active   18d
ns1           Active   9s

```

查看edit namespace yaml 

 kubectl edit ns ns1

```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: 2021-12-09T08:47:46Z
  name: ns1
  resourceVersion: "2465607"
  selfLink: /api/v1/namespaces/ns1
  uid: aea30187-58cc-11ec-96ad-0202ee5eebb3
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
~
~
~
~

```

kubectl get ns ns1 -o yaml

kubectl explain namespace  


```
[root@slave centos]# kubectl explain namespace
KIND:     Namespace
VERSION:  v1

DESCRIPTION:
     Namespace provides a scope for Names. Use of multiple namespaces is
     optional.

FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#resourc

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#types-k

   metadata     <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#metadat

   spec <Object>
     Spec defines the behavior of the Namespace. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-an

   status       <Object>
     Status describes the current status of a Namespace. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#spec-an


```
查看下一级 

 kubectl explain namespace.metadata 
 

yaml 创建namespace 

```
[root@slave centos]# vim ns2-create.yaml
[root@slave centos]# cat ns2-create.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ns2

```


```
[root@slave centos]# kubectl create -f ns2-create.yaml
namespace/ns2 created
 
[root@slave centos]# kubectl get ns
NAME          STATUS   AGE
bass1         Active   18d
bass2         Active   16d
ca-baas1      Active   18d
default       Active   18d
kube-public   Active   18d
kube-system   Active   18d
ns1           Active   15m
ns2           Active   24s

```



删除ns


```
[root@slave centos]# kubectl delete ns ns1
namespace "ns1" deleted

```
用yaml删除ns


```
[root@slave centos]# kubectl delete -f ns2-create.yaml
namespace "ns2" deleted

```
