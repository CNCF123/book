## 安装和配置指南

Harbor 可以通过以下两种方式之一安装：

* 在线安装程序：
  安装程序从Docker集线器下载Harbour的映像。因此，安装程序的尺寸非常小。
* 脱机安装程序：
  当主机没有Internet连接时，请使用此安装程序。安装程序包含预制图像，因此其大小较大。

本指南介绍使用在线或离线安装程序安装和配置Harbor的步骤。安装过程几乎相同。

如果您运行以前版本的Harbor，则可能需要更新`harbor.cfg`和迁移数据以适应新的数据库模式。详情请参阅[Harbor Migration Guide](https://github.com/vmware/harbor/blob/master/docs/migration_guide.md).。

此外，Kubernetes的部署指导是社区创建的。有关详细信息，请参阅[Harbor on Kubernetes](https://github.com/vmware/harbor/blob/master/docs/kubernetes_deployment.md) 。

#### 先决条件

Harbor 部署为几个Docker容器，因此可以部署在任何支持Docker的Linux发行版上。目标主机需要安装Python，Docker和Docker Compose。

* Python应该是2.7或更高版本。请注意，您可能必须在Linux发行版（Gentoo，Arch）上安装Python，该版本不附带默认安装的Python解释器（2017.6.9补充：Python3 版本会报错，请用2.7版本）
* Docker引擎应为1.10或更高版本。有关安装说明，请参阅：
  [https：](https://docs.docker.com/engine/installation/)
  [//docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)
* Docker Compose需要为1.6.0或更高版本。有关安装说明，请参阅：
  [https：](https://docs.docker.com/compose/install/)
  [//docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)



