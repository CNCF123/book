## worker节点

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

`sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config`

5.禁止使用 swap

`echo "vm.swappiness=0" >> /etc/sysctl.d/k8s.conf`

`sysctl -p`

6.开启ipvs模块

yum install -y ipvsadm conntrack  ipset jq sysstat libseccomp

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

#### step2

1.安装docker-ce

[http://www.dockerk8s.net/docker/2install/2centos.html](#)

2.设置镜像加速

[http://www.dockerk8s.net/docker/3image/2image-add-speed.html](#)

3.设置这两个参数为1，开启网桥

`echo "net.bridge.bridge-nf-call-iptables=1" >>`/etc/sysctl.d/k8s.conf

`echo "net.bridge.bridge-nf-call-ip6tables=1" >>`/etc/sysctl.d/k8s.conf

`echo "net.ipv4.ip_forward=1" >>`/etc/sysctl.d/k8s.conf

`sysctl -p`

4.从docker1.13开始，iptables的FORWARD的默认规则为DROP，这可能影响k8s的报文转发功能，修改为ACCEPT

方法：

yum install -y iptables

修改 vim /usr/lib/systemd/system/docker.service，在“ExecStart=/usr/bin/dockerd”的**下面**，添加一行

`ExecStartPost=/usr/sbin/iptables -P FORWARD ACCEPT`

5.docker启动，开机自启动

`systemctl daemon-reload`

`systemctl start docker`

`systemctl enable docker`

#### step3

1.添加 kubernetes的yum源

vim /etc/yum.repos.d/kubernetes.repo

\[kubernetes\]

name=kubernetes repo

baseurl=[https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86\_64/](https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/)

gpgcheck=0

enabled=1

2.安装k8s的相关组件

`yum -y install kubelet kubeadm`

3.配置kubelet配置文件，关闭swap

在k8s 1.8版本开始，强制要求关闭系统的swap交换分区，否则kubelet无法安装

编辑 vim /etc/sysconfig/kubelet, 用来忽略禁止使用swap的限制

`KUBELET_EXTRA_ARGS="--fail-swap-on=false"`

**注意：先不启动**

先设置开机自启动 `systemctl enable kubelet`

#### step4

先拉取镜像：

* node节点需要 kube-proxy、pause 、quay.io/coreos/flannel的镜像，需要拉取，注意重新打标签tag，参考解决k8s.gcr.io的镜像问题
* 如果 flannel的镜像pull慢的话，可以到github下载 flanneld-版本-amd64.docker的镜像文件，然后使用 docker load -i flanneld-版本-amd64.docker

添加node节点，在node节点上执行，加入集群的命令：

`kubeadm join 172.16.0.53:6443 --token d387y7.j5na40ast2iz162h --discovery-token-ca-cert-hash sha256:dab4f81996be52ac17160bff6943fa6eddd73e2cdb8c9343751ea0bde083087f`

#### step5

* 查看集群信息，在master节点上执行命令

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

