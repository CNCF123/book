## [安装方法](https://www.alibabacloud.com/help/zh/doc-detail/44778.htm)

### 获取 ECS Driver

您可以直接下载阿里云 ECS Driver 编译结果。以下版本可以配合 Docker Machine 0.5 及更高版本使用。

* Windows 64 bit: [docker-machine-driver-aliyunecs\_windows-amd64.tgz](https://docker-machine-drivers.oss-cn-beijing.aliyuncs.com/docker-machine-driver-aliyunecs_windows-amd64.tgz)

* Linux 64 bit: [docker-machine-driver-aliyunecs\_linux-amd64.tgz](https://docker-machine-drivers.oss-cn-beijing.aliyuncs.com/docker-machine-driver-aliyunecs_linux-amd64.tgz)

* Mac OSX 64 bit: [docker-machine-driver-aliyunecs\_darwin-amd64.tgz](https://docker-machine-drivers.oss-cn-beijing.aliyuncs.com/docker-machine-driver-aliyunecs_darwin-amd64.tgz)

### 安装 ECS Driver

Docker Machine 要求 ECS Driver 的 binary 在系统路径（以 Mac OSX 为例，就是`$PATH`包含的路径）下，将文件名改为`docker-machine-driver-aliyunecs`并添加执行权限即可，这样 Docker Machine 可以直接访问到。

**Mac OSX** 下安装命令其实就是将 ECS Driver 的 binary 拷贝到了`/usr/local/bin`目录下。

命令如下所示：

`mkdir docker-machine`

`# Download and unzip Aliyun ECS driver`

`curl -L https://docker-machine-drivers.oss-cn-beijing.aliyuncs.com/docker-machine-driver-aliyunecs_darwin-amd64.tgz > driver-aliyunecs.tgz && tar xzvf driver-aliyunecs.tgz -C docker-machine && rm driver-aliyunecs.tgz`

`mv docker-machine/bin/* /usr/local/bin`

`mv /usr/local/bin/docker-machine-driver-aliyunecs.darwin-amd64 /usr/local/bin/docker-machine-driver-aliyunecs && chmod +x /usr/local/bin/docker-machine-driver-aliyunecs`

