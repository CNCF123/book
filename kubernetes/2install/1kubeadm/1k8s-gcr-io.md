## 解决k8s.gcr.io问题

新版本的Kubernetes在安装部署中，需要从k8s.grc.io仓库中拉取所需镜像文件，但由于GFW导致无法正常拉取，本文将介绍如何绕过此问题，来完成业务的部署。

```
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/kube-apiserver:v1.13.2
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/kube-controller-manager:v1.13.2
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/kube-scheduler:v1.13.2
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/kube-proxy:v1.13.2
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/pause:3.1
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/etcd:3.2.24
    [ERROR ImagePull]: failed to pull image k8s.gcr.io/coredns:1.2.6
```

### 解决方案

* docker.io仓库对google的容器做了镜像，可以通过下列命令下拉取相关镜像：

`docker pull mirrorgooglecontainers/kube-apiserver:v1.13.2`

`docker pull mirrorgooglecontainers/kube-controller-manager:v1.13.2`

`docker pull mirrorgooglecontainers/kube-scheduler:v1.13.2`

`docker pull mirrorgooglecontainers/kube-proxy:v1.13.2`

`docker pull mirrorgooglecontainers/pause:3.1`

`docker pull mirrorgooglecontainers/etcd:3.2.24`

`docker pull coredns/coredns:1.2.6`

* 通过**docker tag**命令来修改镜像的标签：

`docker tag docker.io/mirrorgooglecontainers/kube-apiserver:v1.13.2  k8s.gcr.io/kube-apiserver:v1.13.2`

`docker tag docker.io/mirrorgooglecontainers/kube-controller-manager:v1.13.2  k8s.gcr.io/kube-controller-manager:v1.13.2`

`docker tag docker.io/mirrorgooglecontainers/kube-scheduler:v1.13.2  k8s.gcr.io/kube-scheduler:v1.13.2`

`docker tag docker.io/mirrorgooglecontainers/kube-proxy:v1.13.2  k8s.gcr.io/kube-proxy:v1.13.2`

`docker tag docker.io/mirrorgooglecontainers/pause:3.1  k8s.gcr.io/pause:3.1`

`docker tag docker.io/mirrorgooglecontainers/etcd:3.2.24  k8s.gcr.io/etcd:3.2.24`

`docker tag docker.io/coredns/coredns:1.2.6  k8s.gcr.io/coredns:1.2.6`

