## 镜像加速

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，强烈建议安装 Docker 之后配置镜像加速。

使用 --registry-mirror 配置 Docker 守护进程

您可以配置 Docker 守护进程默认使用 Docker 官方镜像加速。这样您可以默认通过官方镜像加速拉取镜像，而无需在每次拉取时指定 registry.docker-cn.com。

您可以在 Docker 守护进程启动时传入 --registry-mirror 参数：

\# docker --registry-mirror=[https://registry.docker-cn.com](https://registry.docker-cn.com) daemon

对于使用CentOS7的系统:

`# mkdir /etc/docker`

`# vim /etc/docker/daemon.json`中写入如下内容（如果文件不存在请新建该文件）

```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```

建议：使用阿里云镜像加速

镜像加速资源：

网易：[http://hub-mirror.c.163.com](http://hub-mirror.c.163.com)

阿里云：[https://xxx.mirror.aliyuncs.com](https://xxx.mirror.aliyuncs.com)

中科大：[https://docker.mirrors.ustc.edu.cn](https://docker.mirrors.ustc.edu.cn)

