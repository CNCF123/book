# [使用方法](https://www.alibabacloud.com/help/zh/doc-detail/44779.htm)

## 配置环境变量 {#h2-u914Du7F6Eu73AFu5883u53D8u91CF1}

通过配置环境变量，可以简化 Docker Machine 命令里每次都要用到的参数。环境变量的参考配置如下所示。

* `export DEBUG=true `
* `export ECS_ACCESS_KEY_ID=<your_access_key_id> `
* `export ECS_ACCESS_KEY_SECRET=<your_access_key_secret> `
* `export ECS_REGION=<your_ecs_region>`
* `export ECS_SSH_PASSWORD=<your_ssh_password> `
* `export MACHINE_DOCKER_INSTALL_URL= http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet`
* `# Optional for VPC only`
* `export ECS_VPC_ID=<your_vpc_id>`
* `export ECS_VSWITCH_ID=<your_vswitchid>`

**注意：**

* `ECS_REGION`是您的 ECS 实例的地域，例如，cn-beijing，cn-hangzhou，cn-qingdao。

* `ECS_SSH_PASSWORD`是您之前设置的 ECS 机器 SSH 登录的密码。密码可以包含 8~30 个字符，必须同时包含以下三种字符（大/小写字母、数字和特殊符号\)，且不支持反斜杠（\）和双引号（”）。详细信息参见 ECS 实例的[相关文档](https://www.alibabacloud.com/help/zh/doc-detail/25499.htm)。

* 由于 Docker Engine 的官方 repo 在国内访问非常不稳定，您需要将`MACHINE_DOCKER_INSTALL_URL`环境变量配置到如上所示的阿里云镜像站点；否则，在创建机器时可能会出现`Error Creating machine: Error running provisioning: error installing docker`之类的错误。在阿里云内网，可以使用[http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/intranet](http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/intranet)作为 Docker Engine 的安装镜像。

* 如果没有 VPC 相关配置，请不要配置`ECS_VPC_ID`和`ECS_VSWITCH_ID`这两个环境变量。如果您有 VPC 相关账号，请确保您当前的`ECS_REGION`支持 VPC 配置。

## 使用 ECS Driver 创建或删除 ECS 机器 {#h2--ecs-driver-ecs-2}

配置过环境变量后，大部分参数使用环境变量默认就可以了。

您可以使用以下命令创建一台带有 Docker 环境的 ECS 实例。

```
docker-machine create -d aliyunecs dev1
```

示例输出结果如下所示。

```
$ docker-machine create -d aliyunecs dev1
Running pre-create checks...
Creating machine...
Waiting for machine to be running, this may take a few minutes...
Machine is running, waiting for SSH to be available...
Detecting operating system of created instance...
Provisioning created instance...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
To see how to connect Docker to this machine, run: docker-machine env dev1
```

您可以使用`docker-machine ls`命令查看创建的包含 Docker 环境的机器的情况。

```
$ docker-machine ls
NAME    ACTIVE    DRIVER        STATE        URL                     SWARM
dev1    -        aliyunecs   Running        tcp://1.2.7.2:9376
```

您可以使用`docker-machine kill`命令停止此机器，并查看机器状态。

```
$ docker-machine kill dev1
$ docker-machine ls
NAME   ACTIVE   DRIVER      STATE     URL                             SWARM
dev1   -        aliyunecs   Stopped   tcp://1.2.7.2:9376
```

您可以使用`docker-machine rm`命令删除此机器（也可以不停止机器直接删除机器），并查看机器信息。

```
$ docker-machine rm dev1
Successfully removed dev1
$ docker-machine ls
NAME   ACTIVE   DRIVER   STATE   URL   SWARM
```

更多`docker-machine`命令可以参考官方[machine subcommands reference](http://docs.docker.com/machine/reference/)。

