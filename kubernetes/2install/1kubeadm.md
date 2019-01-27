## kubeadm安装k8s

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

`    /sbin/modinfo -F filename $i &> /dev/null`

`    if [ $? -eq 0 ]; then`

`        /sbin/modprobe $i`

`    fi`

`done`



