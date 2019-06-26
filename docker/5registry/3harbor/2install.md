## 安装步骤

#### 1.下载安装包

[https://github.com/goharbor/harbor/releases](https://github.com/goharbor/harbor/releases)

#### 2.安装依赖工具

docker:

安装docker步骤跳过

docker-compose:

yum -y install docker-compose

#### 3.解压安装包

tar -xvf harbor-offline-installer-v1.8.1.tgz

#### 4.修改配置文件harbor.yaml

hostname：目标主机的主机名，用于访问UI和注册服务。不能使用localhost和127.0.0.1，因为harbor需要被外部客户端访问，我这里修改成了IP地址。

max\_job\_workers：作业服务中的最大复制worker数，这里默认写的10，我修改成了2。

customize\_crt：设置为on，prepare脚本创建用于生成/验证注册表令牌的私钥和根证书。如果设置成off，密钥和根证书将由外部源提供，我设置的是on。

certificate：SSL证书的位置，只有协议设置成**https**的时候，这个属性才会生效。

private\_key：SSL秘钥的位置，只有协议设置成**https**的时候，这个属性才会生效。

secretkey\_path：密码存放的路径，这里最好别修改，否则后面会报错，我修改成了【/data/admin/】。

log\_rotate\_count：日志文件保留的数量，达到最大值后会循环删除之前的日志。

log\_rotate\_size：每个日志的大小，我为了节省空间设置日志最多保留5个，每个最大200MB。

db\_password：用于DB身份验证的MySQL数据库的根密码。

#### 5.安装并启动

./install.sh

\[Step 0\]: checking installation environment ...

Note: docker version: 18.09.6

Note: docker-compose version: 1.18.0

\[Step 1\]: loading Harbor images ...  
ba58b7bb3f17: Loading layer \[==================================================&gt;\]  33.32MB/33.32MB  
1351a2c39b77: Loading layer \[==================================================&gt;\]  8.967MB/8.967MB  
13b531e88128: Loading layer \[==================================================&gt;\]  46.85MB/46.85MB  
664abb325748: Loading layer \[==================================================&gt;\]  5.632kB/5.632kB  
9f20c7a04d55: Loading layer \[==================================================&gt;\]  27.14kB/27.14kB  
594e8fcaaae3: Loading layer \[==================================================&gt;\]  46.85MB/46.85MB

等待一会

✔ ----Harbor has been installed and started successfully.----

Now you should be able to visit the admin portal at [http://harbor.chenkaidi.com](http://harbor.chenkaidi.com).  
For more details, please visit [https://github.com/goharbor/harbor](https://github.com/goharbor/harbor) .

successfully显示成功

#### 6.登录

[http://harbor.chenkaidi.com](http://harbor.chenkaidi.com)

#### 7.添加信任

在其他docker主机的/etc/docker/daemon.json文件，添加对这台主机的信任

"insecure-registries": \["harbor.chenkaidi.com","192.168.1.1"\]

