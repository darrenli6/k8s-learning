由imagePullPolicy参数控制

- Always : 不管本地有没有镜像，都要从仓库中下载镜像
- Never : 从来不从仓库下载镜像,   只用本地镜像,本地没有就算了
- IfNotPresent: 如果本地存在就直接使用, 不存在才从仓库下载
默认的策略是：
当镜像标签版本是latest，默认策略就是Always
如果指定特定版本默认拉取策略就是IfNotPresent



