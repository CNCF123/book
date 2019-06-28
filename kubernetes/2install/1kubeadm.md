## kubeadm安装k8s

说明：kubelet和docker使用systemd服务运行，其他组件都是以Pod资源的形式运行。

准备工作：

一个master节点，二个node节点

OS: CentOS7.6

Container: Docekr CE 1806

Kubernetes: 1.13.2

`172.16.0.53 master01`

`172.16.0.54 node01`

`172.16.0.55 node02`

节点ip根据实际情况

