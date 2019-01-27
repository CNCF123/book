## kubeadm安装k8s

说明：kubelet和docker使用systemd服务运行，其他组件都是以Pod资源的形式运行。

#### step1

准备一个master节点，二个node节点

OS: CentOS7.6

Container: Docekr CE 1809

Kubernetes: 1.12

#### step2

1.主机名解析

在master和node节点添加解析信息

`172.16.0.53 master01`

`172.16.0.54 node01`

`172.16.0.55 node02`

2.时间同步

`systemctl start chronyd`

`systemctl enable chronyd`

3.关闭防火墙

`systemctl stop firewalld`

`systemctl disable firewalld`

`systemctl stop iptables`

`systemctl disable iptables`

4.关闭selinux

`setenforce 0`

编辑 vim /etc/sysconfig/selinux 设置为 disabled

5.禁止使用 swap

`swapoff  -a`

6.开启ipvs模块

编辑 vim /etc/sysconfig/modules/ipvs.modules

`#!/bin/bash`

`ipvs_mods_dir="/usr/lib/modules/$(uname -r)/kernel/net/netfilter/ipvs"`

`for i in $(ls $ipvs_mods_dir | grep -o "^[^.]*"); do`

`/sbin/modinfo -F filename $i &> /dev/null`

`if [ $? -eq 0 ]; then`

`/sbin/modprobe $i`

`fi`

`done`

#### step3

1.在master和node节点安装docker-ce

`cd /etc/yum.repos.d/`

`wget   wget`[`https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`](https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo)

`yum -y install docker-ce`

设置[镜像加速](http://www.dockerk8s.net/docker/3image/2image-add-speed.html)

确认这两个参数为1

cat /proc/sys/net/bridge/bridge-nf-call-iptables

1

cat /proc/sys/net/bridge/bridge-nf-call-ip6tables

1

从docker1.13开始，iptables的FORWARD的默认规则为DROP，这可能影响k8s的报文转发功能，修改为ACCEPT

方法：

修改 vim /usr/lib/systemd/system/docker.service，在“ExecStart=/usr/bin/dockerd”的下面，添加一行

ExecStartPost=/usr/sbin/iptables -P FORWARD ACCEPT

启动，开机自启动

`systemctl daemon-reload`

`systemctl start docker`

`systemctl enable docker`

#### step4

安装k8s的相关组件

master节点：

`yum -y install kubelet kubeadm kubectl`

node节点：

`yum -y install kubelet kubeadm`

配置kubelet配置文件

在k8s 1.8版本开始，强制要求关闭系统的swap交换分区，否则kubelet无法安装

编辑 vim /etc/sysconfig/kubelet, 用来忽略禁止使用swap的限制

`KUBELET_EXTRA_ARGS="--fail-swap-on=false"`

设置开机自启动

systemctl eabled kubelet

#### step5

kubeadm初始化，有二种方法

方法一：

使用命令行初始化：

kubeadm init --kubernetes-version=v1.13.2 \

--pod-network-cidr=10.244.0.0/16 \

--service-cidr=10.96.0.0/12 \

--apiserver-advertise-address=0.0.0.0 \

--ignore-preflight-errors=Swap

方法二：

