## livenessProbe

  
应用程序长时间持续运行后会逐渐转为不可用状态，并且仅能通过重启操作恢复，Kubemetes 的容器存活性探视lj机制可发现诸如此类的 问题，并依据探测结果结合重启策。  
目前， Kubernetes的容器支持存活性探测方法有三种: ExecAction、TCPSocketAction和HTTPGetAction。

#### ExecAction



#### TCPSocketAction



#### HTTPGetAction





