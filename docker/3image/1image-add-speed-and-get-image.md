## 镜像加速

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，强烈建议安装 Docker 之后配置镜像加速。

使用 --registry-mirror 配置 Docker 守护进程

您可以配置 Docker 守护进程默认使用 Docker 官方镜像加速。这样您可以默认通过官方镜像加速拉取镜像，而无需在每次拉取时指定 registry.docker-cn.com。

您可以在 Docker 守护进程启动时传入 --registry-mirror 参数：

\# docker --registry-mirror=[https://registry.docker-cn.com](https://registry.docker-cn.com) daemon

对于使用[systemd](https://www.freedesktop.org/wiki/Software/systemd/)的系统，请在`/etc/docker/daemon.json`中写入如下内容（如果文件不存在请新建该文件）

```
{
  "registry-mirrors": [
    "https://5cs233bb.mirror.aliyuncs.com"
  ]
}
```

注意：建议使用阿里云镜像加速

备注：本人阿里云镜像加速地址：[https://5cs233bb.mirror.aliyuncs.com](https://5cs233bb.mirror.aliyuncs.com)

## 获取镜像 {#获取镜像}

[Docker Hub](https://hub.docker.com/explore/)上有大量的镜像可以用。

从 Docker 镜像仓库获取镜像的命令是`docker pull`。

其命令格式为：

```
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

可以通过`docker pull --help`命令看到，镜像名称的格式如下：

* Docker 镜像仓库地址：地址的格式一般是`<域名/IP>[:端口号]`。
* **默认地址是 Docker Hub**。
* 仓库名：如之前所说，这里的仓库名是两段式名称，即`<用户名>/<软件名>`。
* 对于 Docker Hub，如果不给出用户名，则默认为`library`，也就是官方镜像。

例如：

```
docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
bf5d46315322: Pull complete
9f13e0ac480c: Pull complete
e8988b5b3097: Pull complete
40af181810e7: Pull complete
e6f7c7e5c03e: Pull complete
Digest: sha256:147913621d9cdea08853f6ba9116c2e27a3ceffecf3b492983ae97c3d643fbbe
Status: Downloaded newer image 
for
 ubuntu:16.04
```

上面的命令中没有给出 Docker 镜像仓库地址，因此将会从 Docker Hub 获取镜像。而镜像名称是`ubuntu:16.04`，因此将会获取官方镜像`library/ubuntu`仓库中标签为`16.04`的镜像。

从下载过程中可以看到我们之前提及的分层存储的概念，镜像是由多层存储所构成。下载也是一层层的去下载，并非单一文件。下载过程中给出了每一层的 ID 的前 12 位。并且下载结束后，给出该镜像完整的`sha256`的摘要，以确保下载一致性。

在使用上面命令的时候，你可能会发现，你所看到的层 ID 以及`sha256`的摘要和这里的不一样。这是因为官方镜像是一直在维护的，有任何新的 bug，或者版本更新，都会进行修复再以原来的标签发布，这样可以确保任何使用这个标签的用户可以获得更安全、更稳定的镜像。

## 



