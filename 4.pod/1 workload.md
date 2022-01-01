

### workloads分为pod与controllers

pod通过控制器实现应用的运行，如何伸缩，升级等
controllers 在集群中管理pod
pod与控制器之间通过label-selector相关联，是唯一的关联方式

比如在pod的YAML里指定

```
labels:
   app: nginx
```
在控制器的YAML里指定


```
selector:
   matchLabels:
   app: nginx
```

