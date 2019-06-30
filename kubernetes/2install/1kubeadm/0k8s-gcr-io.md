## 解决k8s.gcr.io问题

新版本的Kubernetes在安装部署中，需要从k8s.grc.io仓库中拉取所需镜像文件，但由于GFW\(某墙\)导致无法正常拉取，本文将介绍如何绕过此问题，来完成业务的部署。

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

#### 使用GCR Proxy Cache从gcr.io下载镜像

docker pull gcr.azk8s.cn/google\_containers/kube-apiserver:v1.13.2

docker pull gcr.azk8s.cn/google\_containers/kube-controller-manager:v1.13.2

docker pull gcr.azk8s.cn/google\_containers/kube-scheduler:v1.13.2

docker pull gcr.azk8s.cn/google\_containers/kube-proxy:v1.13.2

docker pull gcr.azk8s.cn/google\_containers/pause:3.1

docker pull gcr.azk8s.cn/google\_containers/etcd:3.2.24

docker pull gcr.azk8s.cn/google\_containers/coredns:1.2.6

#### 使用tag命令打标签

docker tag gcr.azk8s.cn/google\_containers/kube-apiserver:v1.13.2  k8s.gcr.io/kube-apiserver:v1.13.2

docker tag gcr.azk8s.cn/google\_containers/kube-controller-manager:v1.13.2  k8s.gcr.io/kube-controller-manager:v1.13.2

docker tag gcr.azk8s.cn/google\_containers/kube-scheduler:v1.13.2  k8s.gcr.io/kube-scheduler:v1.13.2

docker tag gcr.azk8s.cn/google\_containers/kube-proxy:v1.13.2  k8s.gcr.io/kube-proxy:v1.13.2

docker tag gcr.azk8s.cn/google\_containers/pause:3.1  k8s.gcr.io/pause:3.1

docker tag gcr.azk8s.cn/google\_containers/etcd:3.2.24  k8s.gcr.io/etcd:3.2.24

docker tag gcr.azk8s.cn/google\_containers/coredns:1.2.6  k8s.gcr.io/coredns:1.2.6





