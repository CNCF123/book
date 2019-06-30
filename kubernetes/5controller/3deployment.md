## Deployment

Deployment是Kubemetes v1.2引入的新概念，引入的目的是为了更好地解决Pod的编排题 。Deployment在内部使用了 ReplicaSet来实现目的，无论从Deployment的Yaml 定义，还是从它的具体命令行操作来看，我们都可以把它看作 RC和ReplicaSet的一次升级， 两者的相似大概90%。

Deployment相对于RC的一个最大升级是我们可以随时知道当前Pod “部署”的进度。实 际上由于一个 Pod 的创建、调度、绑定节点及在目标 Node 上启动对应的容器这一完整过程需 要一定的时间，所以我们期待系统启动 N个 Pod副本的目标状态，实际上是一个连续变化的“部 署过程”导致的最终状态。

