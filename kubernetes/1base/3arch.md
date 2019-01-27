## 架构

![](/assets/k8s-arch-1.png)Kubernetes 集群由分布式存储 etcd、控制节点master controller 以及服务节点 Node 组成。

master控制节点主要负责整个集群的管理，比如容器的调度、维护资源的状态、自动扩展以及滚动更新等。

node服务节点是真正运行容器的主机，负责管理镜像和容器以及 cluster 内的服务发现和负载均衡。

etcd 集群保存了整个集群的状态。

