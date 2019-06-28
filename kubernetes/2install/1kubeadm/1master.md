## master节点

#### step1

1.主机名解析

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

添加执行权限

chmod +x /etc/sysconfig/modules/ipvs.modules

执行

/etc/sysconfig/modules/ipvs.modules

#### step3

1.安装docker-ce

[http://www.dockerk8s.net/docker/2install/2centos.html](http://www.dockerk8s.net/docker/2install/2centos.html)

2.设置镜像加速

[http://www.dockerk8s.net/docker/3image/2image-add-speed.html](http://www.dockerk8s.net/docker/3image/2image-add-speed.html)

3.设置这两个参数为1，开启网桥

`echo "net.bridge.bridge-nf-call-iptables=1" >> /etc/sysctl.conf`

`echo "net.bridge.bridge-nf-call-ip6tables=1" >> /etc/sysctl.conf`

`sysctl -p`

4.从docker1.13开始，iptables的FORWARD的默认规则为DROP，这可能影响k8s的报文转发功能，修改为ACCEPT

方法：

修改 vim /usr/lib/systemd/system/docker.service，在“ExecStart=/usr/bin/dockerd”的**下面**，添加一行

`ExecStartPost=/usr/sbin/iptables -P FORWARD ACCEPT`

5.docker启动，开机自启动

`systemctl daemon-reload`

`systemctl start docker`

`systemctl enable docker`

#### step4

1.添加 kubernetes的yum源

vim /etc/yum.repos.d/kubernetes.repo

\[kubernetes\]

name=kubernetes repo

baseurl=[https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86\_64/](https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/)

gpgcheck=0

enabled=1

2.安装k8s的相关组件

`yum -y install kubelet kubeadm kubectl`

3.配置kubelet配置文件，关闭swap

在k8s 1.8版本开始，强制要求关闭系统的swap交换分区，否则kubelet无法安装

编辑 vim /etc/sysconfig/kubelet, 用来忽略禁止使用swap的限制

`KUBELET_EXTRA_ARGS="--fail-swap-on=false"`

**注意：先不启动**

先设置开机自启动 `systemctl enable kubelet`

#### step5

kubeadm初始化

使用命令行初始化：

kubeadm init --kubernetes-version=v**1.13.2** \

--pod-network-cidr=10.244.0.0/16 \

--service-cidr=10.96.0.0/12 \

--apiserver-advertise-address=0.0.0.0 \

--ignore-preflight-errors=Swap

备注：解决k8s.gcr.io的镜像问题，请看[http://www.dockerk8s.net/kubernetes/2install/1kubeadm/0k8s-gcr-io.html](http://www.dockerk8s.net/kubernetes/2install/1kubeadm/0k8s-gcr-io.html)

#### step6

1.配置kubectl的配置文件

默认情况下，kubectl会从当前用户的.kube目录下读取名称为config的配置文件，该文件包括集群、用户认证的证书和令牌等信息

在集群初始化时，在/etc/kubetnetes/admin.conf已经保存了这些信息，只需要复制该文件即可

mkdir -p $HOME/.kube

cp -i /etc/kubenetes/admin.conf  $HOEM/.kube/config

chown $\(id -u\):$\(id -g\) $HOME/.kube/config

2.保存加入集群的命令

`kubeadm join 172.16.0.53:6443 --token d387y7.j5na40ast2iz162h --discovery-token-ca-cert-hash sha256:dab4f81996be52ac17160bff6943fa6eddd73e2cdb8c9343751ea0bde083087f`

此时此刻，master节点配置完毕

`kubectl get nodes`

`NAME       STATUS     ROLES    AGE   VERSION`

`master01   NotReady   master   75m   v1.13.2`

#### step7

部署网络插件CNI，这里使用的是fannel

kubectl apply -f [https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)

