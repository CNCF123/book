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
