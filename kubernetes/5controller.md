## 控制器controller

Master节点的各组件中，APIServer仅负责将资源存储于etcd中，并将其变动通知给各相关的客户端程序，如kubelet、kube-scheduler、kube-proxy和kube-controlier-manager等，kube剖heduler监控到处于未绑定状态的Pod对象出现时遂启动调度器为其挑选适配的工作节点，然而，Kubemetes 的核心功能之一还在于要确保各资源对象的当前状态\( status\)以匹配用 户期望的状态\( spec\)，使当前状态不断地向期望状态“和解”\( reconciliation\)来完成容器应用 管理，而这些则是 kube-controIler-manager的任务。kube-controlier-manager是一个独立的单体 守护进程，然而它包含了众多功能不同的控制器类型分别用于各类和解任务。

常见的此类控制器有 Replication Controller、ReplicaSet、Deployment、DaemonSet、StatefulSet、Job和 CronJob等，它们分别以 不同的方式管理Pod资源对象。实践中，对Pod对象的管理通常都是由某种控制器的特定对象来实现的，包括其创建、删除及重新调度等操作。本章将逐一讲解常用的Pod控制器资源。

| replicationcontrollers | rc |  | ReplicationController |
| :--- | :--- | :--- | :--- |
| daemonsets | ds | apps | DaemonSet |
| deployments | deploy | apps | Deployment |
| replicasets | rs | apps | ReplicaSet |
| statefulsets | sts | apps | StatefulSet |
| cronjobs | cj | batch | CronJob |
| jobs |  | batch | Job |
| ingresses | ing | extensions | Ingress |



