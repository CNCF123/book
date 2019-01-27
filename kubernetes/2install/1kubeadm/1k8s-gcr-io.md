## [解决k8s.gcr.io问题](https://blog.csdn.net/jinguangliu/article/details/82792617 )

新版本的Kubernetes在安装部署中，需要从k8s.grc.io仓库中拉取所需镜像文件，但由于GFW导致无法正常拉取，本文将介绍如何绕过此问题，来完成业务的部署。



\[preflight/images\] You can also perform this action in beforehand using 'kubeadm config images pull'

\[preflight\] Some fatal errors occurred:

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/kube-apiserver-amd64:v1.11.3\]: exit status 1

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/kube-controller-manager-amd64:v1.11.3\]: exit status 1

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/kube-scheduler-amd64:v1.11.3\]: exit status 1

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/kube-proxy-amd64:v1.11.3\]: exit status 1

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/pause:3.1\]: exit status 1

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/etcd-amd64:3.2.18\]: exit status 1

	\[ERROR ImagePull\]: failed to pull image \[k8s.gcr.io/coredns:1.1.3\]: exit status 1

\[preflight\] If you know what you are doing, you can make a check non-fatal with \`--ignore-preflight-errors=...\`

### 解决方案

docker.io仓库对google的容器做了镜像，可以通过下列命令下拉取相关镜像：



docker pull mirrorgooglecontainers/kube-apiserver-amd64:v1.11.3

docker pull mirrorgooglecontainers/kube-controller-manager-amd64:v1.11.3

docker pull mirrorgooglecontainers/kube-scheduler-amd64:v1.11.3

docker pull mirrorgooglecontainers/kube-proxy-amd64:v1.11.3

docker pull mirrorgooglecontainers/pause:3.1

docker pull mirrorgooglecontainers/etcd-amd64:3.2.18

docker pull coredns/coredns:1.1.3

版本信息需要根据实际情况进行相应的修改。通过**docker tag**命令来修改镜像的标签：



docker tag docker.io/mirrorgooglecontainers/kube-proxy-amd64:v1.11.3 k8s.gcr.io/kube-proxy-amd64:v1.11.3

docker tag docker.io/mirrorgooglecontainers/kube-scheduler-amd64:v1.11.3 k8s.gcr.io/kube-scheduler-amd64:v1.11.3

docker tag docker.io/mirrorgooglecontainers/kube-apiserver-amd64:v1.11.3 k8s.gcr.io/kube-apiserver-amd64:v1.11.3

docker tag docker.io/mirrorgooglecontainers/kube-controller-manager-amd64:v1.11.3 k8s.gcr.io/kube-controller-manager-amd64:v1.11.3

docker tag docker.io/mirrorgooglecontainers/etcd-amd64:3.2.18  k8s.gcr.io/etcd-amd64:3.2.18

docker tag docker.io/mirrorgooglecontainers/pause:3.1  k8s.gcr.io/pause:3.1

docker tag docker.io/coredns/coredns:1.1.3  k8s.gcr.io/coredns:1.1.3

使用docker rmi删除不用镜像，通过docker images命令显示，已经有我们需要的镜像文件，可以继续部署工作了：



\[root@zookeeper01 jinguang1\]\# docker images

REPOSITORY                                                               TAG                 IMAGE ID            CREATED             SIZE

k8s.gcr.io/kube-proxy-amd64                                              v1.11.3             be5a6e1ecfa6        10 days ago         97.8 MB

k8s.gcr.io/kube-scheduler-amd64                                          v1.11.3             ca1f38854f74        10 days ago         56.8 MB

k8s.gcr.io/kube-apiserver-amd64                                          v1.11.3             3de571b6587b        10 days ago         187 MB

coredns/coredns                                                          1.1.3               b3b94275d97c        3 months ago        45.6 MB

k8s.gcr.io/coredns                                                       1.1.3               b3b94275d97c        3 months ago        45.6 MB

k8s.gcr.io/etcd-amd64                                                    3.2.18              b8df3b177be2        5 months ago        219 MB

k8s.gcr.io/pause                                                         3.1                 da86e6ba6ca1        9 months ago        742 kB

