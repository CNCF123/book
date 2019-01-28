## 工作负载型资源

Pod是工作负载型资源中的基础资源，它负责运行容器，并为其解决环境性的依赖，例 如，向 容器注入共享 的或持久化的 存储卷 、 配置信息或密钥数据等 。 但 Pod 可能会因为资 源、超限或节点故障等原因而终止，这些非正常终止的 Pod 资源需要被重建，不过，这类工 作将由工作负载型的控制器来完成，它们通常也称为 pod控制器。

应用程序分为 无状态和有状态 两种类型，它们对环境的依赖及工作特性有很大的不同， 因此分属两种不同类型的 Pod控制器来管理， ReplicationController、 ReplicaSet和 Deployment 负责管理无状态应用， Statefu!Set用于管控有状态类应用 。 ReplicationController是上一代的 控制器，其功能由 ReplicaSet 和 Deployment 负责实现，因此几近于废弃 。 还有些应用较为 独特，它们需要在集群 中的每个节点上运行单个 Pod资源，负责收集日志或运行系统服务 等任务，这些 Pod资源的管理则属于 DaemonSet控制器的分内之事。 另外，有些容器化应 用需要继续运行以为守护进程不间断地提供服务， 而有些则应该在正常完成后退出 ，这些在 正常完成后就应该退出的容器化应用则由 Job控制器负责管控。 下面是各 Pod控制器更为详 细的说明 。

ReplicationController

用于确保每个 Pod 副本在任-时刻均能满足目标数量 ，换言 之，它用于保证每个容器或容器组总是运行并且可访问;它是上一代的无状态 Pod 应用控制器，建议读者使用新型控制器 Deployment和 ReplicaSet来取代它。

0 ReplicaSet :新一代 ReplicationController， 它与 ReplicationController 的唯一不同之处仅 在于支持的标签选择器不同， R巳plicationController只支持等值选择器，而 ReplicaSet还 额外支持基于集合的选择器 。

口 Deployment:用于 管理无状态的持久 化应用 ， 例如 HTTP 服务 器;它用于为 Pod 和 ReplicaSet提供声明式更新，是建构在 ReplicaSet之上的更为高级的控制器。

口 Statefu!Set :用于管理有状态的持久化应用，如 database服务程序; 其与 D巳ployment 的不同之处在于 StatefulSet会为每个 Pod创建一个独有的持久性标识符，并会确保 各 Pod之间的顺序性。

口DaemonSet:用于确保每个节点都运行了某 Pod的一个副本，新增的节点一样会被添 加此类 Pod ;在节点移除时，此类 Pod 会被回收; DaemonSet 常用于运行集群存储 守护进程一一如 glusterd 和 C巳ph，还有日志收集进程一一如 fluentd 和 logstash，以

仅供\|\|阳I业用途"x交流’苦斗侦川

及监控进程一一一如 Prometheus 的 Node Exporter、 collectd、 Datadog agent 和 Ganglia

的 gmond 等 。

口 Job :用于管理运行 完成后 即可终止的应用，例如批处理作业任 务 ;换句话讲， Job

创建一个或多个 Pod，并确保其符合目标数量，直到 Pod 正常结束而终止 。

