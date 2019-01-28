## 元数据型资源

此类资源、对象用于为集群内部的其他资源配置其行为或特性，如 HorizontalPodAutoscaler资源可用于自动伸缩工作负载类型的资源对象的规模，Pod模板资源可用于为pod资源的创 建预制模板，而 LimitRange 则可为名称空间的资源设置其 CPU 和内存等系统级资源的数量 限制等 。

一个应用通常需要多个资源的支撑，例如，使用 Deployment资源管理应用实例\(Pod\)、一使用ConfigMap资源保存应用配置、使用Service或Ingress资源暴露服务、 使用Volume资 源提供外部存储等 。

