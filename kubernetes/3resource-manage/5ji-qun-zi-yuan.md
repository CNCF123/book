## 集群级资源

Pod、Deployment、Service和 ConfigMap等资源属于名称空间级别，可由相应的项目 管理员所管理。然而，Kubernetes还存在-些集群级别的资源，用于定义集群自身配置信息 的对象，它们仅应该由 集群管理员 进行操作 。集群级资源主要包含 以下几种类型 。

* Namespace

资源对象名称的作用范围，绝大多数对象都隶属于某个名称空间，默认 时隶属于“default”。

* Node

 Kubemetes 集群的工作节点，其标识柯:在当前集群中必须是唯一的 。

* Role

名称空间级别的由规则组成的权限集合，可被RoleBinding 引用 。

* ClusterRole:

Cluster级别的由规则组成的权限集合，可被 RoleBinding 和 ClusterRoleBinding 引用。

* RoleBinding

将Role中的许可权限绑定在一个或一组用户之上，它隶属于且仅能作用于一个名称空间;绑定时，可以引用同一名称空间中的 Role，也可以引用全局名称空间中的 ClusterRole。

* ClusterRoleBinding 

将 ClusterRole 中定义的许可权限绑定在一个或一组用户之上;它能够引用全局名称空间中的 ClusterRole，并能通过 Subject 添加相关信息 。

