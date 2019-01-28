## 资源类型

依据资源的主要功能作为分类标准，Kubemetes的API对象大体可分为工作负载\(Workload\)、发现和负载均衡\(Discovery&LB\)、配置和存储\(Config&Storage\)、集群\(Cluster\)以及元数据\(Metadata\)五个类别。它们基本上都是围绕一个核心目的而设计:如 何更好地运行和丰富Pod资源，从而为容器化应用提供更灵活、更完善的操作与管理组件。

* 工作负载型资源对象    

Pod  Replicaset  Deployments StatefulSets Daemonset Job CronJob

* 服务发现及负载均衡     

Service  Ingress

* 配置与存储    

Volume、Persistent Volume、CSl 、 configmap、  secret

* 集群资源    

Namespace Node Role ClusterRole  RoleBinding  ClusterRoleBinding

* 元数据资源    

HPA PodTemplate LimitRang

