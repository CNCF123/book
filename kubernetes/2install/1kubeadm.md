## kubeadm安装k8s

说明：kubelet和docker使用systemd服务运行，其他组件都是以Pod资源的形式运行。

#### step1

准备一个master节点，二个node节点

OS: CentOS7.6

Container: Docekr CE 1809

Kubernetes: 1.13.2

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

执行

chmod +x /etc/sysconfig/modules/ipvs.modules

/etc/sysconfig/modules/ipvs.modules

#### step3

1.在master和node节点安装docker-ce

`cd /etc/yum.repos.d/`

`wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`

`yum -y install docker-ce`

2.设置[镜像加速](http://www.dockerk8s.net/docker/3image/2image-add-speed.html)

3.从docker1.13开始，iptables的FORWARD的默认规则为DROP，这可能影响k8s的报文转发功能，修改为ACCEPT

方法：

修改 vim /usr/lib/systemd/system/docker.service，在“ExecStart=/usr/bin/dockerd”的**下面**，添加一行

`ExecStartPost=/usr/sbin/iptables -P FORWARD ACCEPT`

4.docker启动，开机自启动

`systemctl daemon-reload`

`systemctl start docker`

`systemctl enable docker`

5.设置这两个参数为1

echo 1 &gt;`/proc/sys/net/bridge/bridge-nf-call-iptables`

echo 1 &gt;`/proc/sys/net/bridge/bridge-nf-call-ip6tables`

#### step4

安装k8s的相关组件

添加 kubernetes的yum源

vim /etc/yum.repos.d/kubernetes.repo

\[kubernetes\]

name=kubernetes repo

baseurl=[https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86\_64/](https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/)

gpgcheck=0

enabled=1

master节点：

`yum -y install kubelet kubeadm kubectl`

node节点：

`yum -y install kubelet kubeadm`

配置kubelet配置文件

在k8s 1.8版本开始，强制要求关闭系统的swap交换分区，否则kubelet无法安装

编辑 vim /etc/sysconfig/kubelet, 用来忽略禁止使用swap的限制

`KUBELET_EXTRA_ARGS="--fail-swap-on=false"`

设置开机自启动

systemctl enable kubelet

#### step5

master节点：

kubeadm初始化

备注：解决k8s.gcr.io的镜像问题，请看下篇文章

使用命令行初始化：

kubeadm init --kubernetes-version=v**1.13.2** \

--pod-network-cidr=10.244.0.0/16 \

--service-cidr=10.96.0.0/12 \

--apiserver-advertise-address=0.0.0.0 \

--ignore-preflight-errors=Swap

#### step6

master节点配置kubectl的配置文件

默认情况下，kubectl会从当前用户的.kube目录下读取名称为config的配置文件，该文件包括集群、用户认证的证书和令牌等信息

在集群初始化时，在/etc/kubetnetes/admin.conf已经保存了这些信息，只需要复制该文件即可

mkdir -p $HOME/.kube

cp -i /etc/kubenetes/admin.conf  $HOEM/.kube/config

chown $\(id -u\):$\(id -g\) $HOME/.kube/config

此时此刻，master节点配置完毕

`kubectl get nodes`

`NAME       STATUS     ROLES    AGE   VERSION`

`master01   NotReady   master   75m   v1.13.2`

#### step7

master节点部署网络插件CNI

kubectl apply -f [https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)

#### step8

添加node节点，在node节点上执行

注意：node节点需要 kube-proxy、pause 、quay.io/coreos/flannel的镜像，需要拉取，注意重新打标签tag，参考解决k8s.gcr.io的镜像问题

如果 flannel的镜像pull慢的话，可以到github下载 flanneld-版本-amd64.docker的镜像文件,

然后使用 docker load -i flanneld-版本-amd64.docker

kubeadm join 172.16.0.53:6443 --token d387y7.j5na40ast2iz162h --discovery-token-ca-cert-hash sha256:dab4f81996be52ac17160bff6943fa6eddd73e2cdb8c9343751ea0bde083087f

#### step9

* 查看集群信息

`kubectl get nodes`

`NAME       STATUS   ROLES    AGE    VERSION`

`master01   Ready    master   136m   v1.13.2`

`node01     Ready    <none>   28m    v1.13.2`

`node02     Ready    <none>   28m    v1.13.2`

* 查看集群pods信息

`kubectl get pods -n kube-system`

`NAME                               READY   STATUS    RESTARTS   AGE    IP            NODE`

`coredns-86c58d9df4-hvjhl           1/1     Running   1          138m   10.244.0.5    master01`

`coredns-86c58d9df4-rrbgn           1/1     Running   1          138m   10.244.0.4    master01`

`etcd-master01                      1/1     Running   1          138m   172.16.0.53   master01`

`kube-apiserver-master01            1/1     Running   2          137m   172.16.0.53   master01`

`kube-controller-manager-master01   1/1     Running   1          137m   172.16.0.53   master01`

`kube-flannel-ds-amd64-d57gf        1/1     Running   2          58m    172.16.0.53   master01`

`kube-flannel-ds-amd64-qprb4        1/1     Running   0          31m    172.16.0.55   node02`

`kube-flannel-ds-amd64-x4wwm        1/1     Running   0          31m    172.16.0.54   node01`

`kube-proxy-9jbqd                   1/1     Running   0          31m    172.16.0.54   node01`

`kube-proxy-9p222                   1/1     Running   0          31m    172.16.0.55   node02`

`kube-proxy-rt6rp                   1/1     Running   1          138m   172.16.0.53   master01`

`kube-scheduler-master01            1/1     Running   2          138m   172.16.0.53   master01`

