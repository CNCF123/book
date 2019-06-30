## ReplicaSet

ReplicaSet 控制器资源启动后会查找集群中匹配其标签选择器的Pod资源对象，当前活动对象的数量与期望的数量不吻合

时， 多则删除，少则补足，不断进行和解循环。

但是，ReplicaSet缺少必要的其他功能，如Pod版本跟新迭代、回滚。我们不应该直接使用底层的ReplicaSet来控制Pod副本，而是

使用管理ReplicaSet的Deployment控制器来控制Pod副本，这是官方的建议。

