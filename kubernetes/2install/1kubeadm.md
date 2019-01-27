## kubeadm安装k8s

#### step1 

准备一个master节点，二个node节点

OS: CentOS7.6

Container: Docekr CE 1809

Kubernetes: 1.12

step2

1.主机名解析

在master和node节点添加解析信息

172.16.0.53 master01

172.16.0.54 node01

172.16.0.55 node02

2.时间同步

systemctl start chronyd

systemctl enable chronyd

3.关闭防火墙

