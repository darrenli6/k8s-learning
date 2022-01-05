[toc]


### pod的生命周期

* pod init container 来初始化环境
* 初始化后，主容器（main container ）开始启动
* 有一个post start的操作
* post start后，开始健康检查 
  *  liveness probe , 用来检查主容器存活状态
  *  readiness probe  用来检查主容器是否启动就绪 

### 容器终止

* 可以在容器停止之前设置 pre stop 操作
* 不能正常销毁pod时候，大概等待30秒会强制终止 
* 终止容器后还能会重启容器 。

### 回顾容器重启策略

* aways 总是重启，默认
* Onfailures: 容器发生错误才会重启，也就是正常终止不会重启
* Never: 表示容器挂了不会重启
