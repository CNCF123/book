## 安装和配置指南

Harbor 可以通过以下两种方式之一安装：

* 在线安装程序：
  安装程序从Docker集线器下载Harbour的映像。因此，安装程序的尺寸非常小。
* 脱机安装程序：
  当主机没有Internet连接时，请使用此安装程序。安装程序包含预制图像，因此其大小较大。

本指南介绍使用在线或离线安装程序安装和配置Harbor的步骤。安装过程几乎相同。

如果您运行以前版本的Harbor，则可能需要更新`harbor.cfg`和迁移数据以适应新的数据库模式。详情请参阅[Harbor Migration Guide](https://github.com/vmware/harbor/blob/master/docs/migration_guide.md).。

此外，Kubernetes的部署指导是社区创建的。有关详细信息，请参阅[Harbor on Kubernetes](https://github.com/vmware/harbor/blob/master/docs/kubernetes_deployment.md) 。



