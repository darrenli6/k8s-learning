[toc]

### healthcheck健康检查

当pod启动时，容器可能会因为某种错误无法访问


方式 | 说明
---|---
liveness probe 存活 |  检查后不健康，重启
readiness probe 就绪 | 检查后不健康，容器设置not ready 


 
#### probe 探测方式


方式 | 说明
---|---
EXEC | 执行命令
httpget | http请求
tcp | tcp连接某一个端口


 
 
