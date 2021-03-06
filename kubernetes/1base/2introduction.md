## kubernetes简介

kubernetes，简称K8s，是用8代替8个字符“ubernete”而成的缩写。是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。

传统的应用部署方式是通过插件或脚本来安装应用。这样做的缺点是应用的运行、配置、管理、所有生存周期将与当前操作系统绑定，这样做并不利于应用的升级更新/回滚等操作，当然也可以通过创建虚拟机的方式来实现某些功能，但是虚拟机非常重，并不利于可移植性。

新的方式是通过部署容器方式实现，每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源。相对于虚拟机，容器能快速部署，由于容器与底层设施、机器文件系统解耦的，所以它能在不同云、不同版本操作系统间进行迁移。

容器占用资源少、部署快，每个应用可以被打包成一个容器镜像，每个应用与容器间成一对一关系也使容器有更大优势，使用容器可以在build或release 的阶段，为应用创建容器镜像，因为每个应用不需要与其余的应用堆栈组合，也不依赖于生产环境基础结构，这使得从研发到测试、生产能提供一致环境。类似地，容器比虚拟机轻量、更“透明”，这更便于监控和管理。

### 特点

* 可移植: 支持公有云，私有云，混合云，多重云（multi-cloud）
* 可扩展: 模块化, 插件化, 可挂载, 可组合
* 自动化: 自动部署，自动重启，自动复制，自动伸缩/扩展

### 组件

#### Master 组件

* 1.kube-apiserver
* 2.ETCD
* 3.kube-controller-manager
* 4.cloud-controller-manager
* 5.kube-scheduler
* 6.插件 addons
* 6.1.CoreDNS
* 6.2.用户界面
* 6.3.容器资源监测
* 6.4.Cluster-level Logging

#### Node 组件

* 1.docker
* 2.kubelet
* 3.kube-proxy
* 4.flannel



