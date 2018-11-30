## 安装和配置指南

Harbor 可以通过以下两种方式之一安装：1

* 在线安装程序：
  安装程序从Docker集线器下载Harbour的映像。因此，安装程序的尺寸非常小。
* 脱机安装程序：
  当主机没有Internet连接时，请使用此安装程序。安装程序包含预制图像，因此其大小较大。

本指南介绍使用在线或离线安装程序安装和配置Harbor的步骤。安装过程几乎相同。

如果您运行以前版本的Harbor，则可能需要更新`harbor.cfg`和迁移数据以适应新的数据库模式。详情请参阅[Harbor Migration Guide](https://github.com/vmware/harbor/blob/master/docs/migration_guide.md).。

此外，Kubernetes的部署指导是社区创建的。有关详细信息，请参阅[Harbor on Kubernetes](https://github.com/vmware/harbor/blob/master/docs/kubernetes_deployment.md) 。

#### 先决条件

Harbor 部署为几个Docker容器，因此可以部署在任何支持Docker的Linux发行版上。目标主机需要安装Python，Docker和Docker Compose。

* Python应该是2.7或更高版本。
* Python解释器（注意：Python3 版本会报错，请用2.7版本）
* Docker引擎应为1.10或更高版本。有关安装说明，请参阅：[docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)
* Docker Compose需要为1.6.0或更高版本。有关安装说明，请参阅：[docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)

## 安装步骤

安装步骤如下

1. 下载安装程序;
2. 配置
   ports.cfg
    ;
3. 运行
   install.sh
   来安装和启动Harbor;

#### 下载安装程序：

可以从发行页面下载安装程序的二进制文件。选择在线或离线安装程序。使用tar命令来提取包。

在线安装：

```
tar xvf harbor-online-installer-<version>.tgz
```

离线安装程序：

```
tar xvf harbor-offline-installer-<version>.tgz
```

#### 配置Harbor

配置参数位于文件harbor.cfg中。

在ports.cfg中有两类参数，必需参数和可选参数。

* 必需参数
  ：需要在配置文件中设置这些参数。如果用户更新它们
  `harbor.cfg`
  并运行
  `install.sh`
  脚本以重新安装Harbor，它们将生效。
* 可选参数
  ：这些参数是可选的。如果他们 配置到
  `harbor.cfg`
  ，他们只能在首次启动Harbor 生效。这些参数的后续更新
  `harbor.cfg`
  将被忽略。海港启动后，用户可以将其留空，并在Web UI上进行更新。
  注意：
  如果您选择通过用户界面设置这些参数，请务必在Harbour启动后立即进行。特别地，您必须在注册或创建任何新的用户之前设置所需的
  auth\_mode
  。当系统中有用户（默认管理员用户除外）时， 无法更改
  auth\_mode
  。

参数如下所述 – 请注意，至少需要更改hostname属性。

##### 必需参数：

* hostname：目标主机的主机名，用于访问UI和注册表服务。它应该是目标机器的IP地址或完全限定域名（FQDN），例如192.168.1.10或reg.yourdomain.com。不要使用localhost或127.0.0.1为主机名 – 注册表服务需要外部客户端访问！

* ui\_url\_protocol：（http或https。默认为http）用于访问UI和令牌/通知服务的协议。如果启用公证，则此参数必须为https。默认情况下，这是http。要设置https协议，请参阅使用HTTPS访问harbor。

* db\_password：用于db\_auth的MySQL数据库的根密码。更改此密码以供任何生产用途！

* max\_job\_workers：（默认值为3）作业服务中的最大复制工作数。对于每个映像复制作业，工作程序将存储库的所有标签同步到远程目标。增加此数字允许系统中更多的并发复制作业。但是，由于每个工作人员都会消耗一定数量的网络/ CPU / IO资源，请根据主机硬件资源选择该属性的值。

* customize\_crt：（打开或关闭，默认为打开）当此属性打开时，准备脚本将为注册表令牌的生成/验证创建私钥和根证书。当密钥和根证书由外部源提供时，将此属性设置为off。有关详细信息，请参阅自定义密钥和harbor令牌服务证书。

* ssl\_cert：SSL证书的路径，仅当协议设置为https时才应用

* ssl\_cert\_key：SSL密钥的路径，仅当协议设置为https时才应用

* secretkey\_path：用于在复制策略中加密或解密远程注册表的密码的密钥路径。

##### 可选参数

* 电子邮件设置：Harbor需要这些参数才能向用户发送“密码重设”电子邮件，只有在需要该功能时才需要这些参数。另外，请注意，在默认情况下SSL连接时没有启用-如果你的SMTP服务器需要SSL，但不支持STARTTLS，那么你应该通过设置启用SSL email\_ssl = TRUE。

* email\_server = smtp.mydomain.com

* email\_server\_port = 25

* email\_username =  
  [sample\_admin@mydomain.com](mailto:sample_admin@mydomain.com)

* email\_password = abc

* email\_from = admin  
  [sample\_admin@mydomain.com](mailto:sample_admin@mydomain.com)

* email\_ssl = false

* harbor\_admin\_password：管理员的初始密码。该密码仅在Harbor 第一次启动时生效。之后，此设置将被忽略，并且应在UI中设置管理员的密码。请注意，默认用户名/密码为admin / Harbor12345。

* auth\_mode：使用的身份验证类型。默认情况下，它是db\_auth，即凭据存储在数据库中。对于LDAP身份验证，请将其设置为ldap\_auth。重要提示：从现有的Harbor 实例升级时，必须确保auth\_modeharbor.cfg在启动新版本的Harbor之前是一样的。否则，升级后用户可能无法登录。

* ldap\_url：LDAP端点URL（例如ldaps://ldap.mydomain.com）。 仅当auth\_mode设置为ldap\_auth时才使用。

* ldap\_searchdn：具有搜索LDAP / AD服务器权限的用户的DN（例如uid=admin,ou=people,dc=mydomain,dc=com）。

* ldap\_search\_pwd：由ldap\_searchdn指定的用户的密码。

* LDAP\_BASEDN：基本DN查找用户，如ou=people,dc=mydomain,dc=com。 仅当auth\_mode设置为ldap\_auth时才使用。

* LDAP\_FILTER：用于查找用户，例如，搜索过滤器\(objectClass=person\)。

* ldap\_uid：用于在LDAP搜索期间匹配用户的属性，它可以是uid，cn，电子邮件或其他属性。

* ldap\_scope：搜索用户的范围，1-LDAP\_SCOPE\_BASE，2-LDAP\_SCOPE\_ONELEVEL，3-LDAP\_SCOPE\_SUBTREE。默认值为3。

* self\_registration：（开或关，默认为开）启用/禁用用户注册自己的能力。禁用时，只能由管理员用户创建新用户，只有管理员用户才能在海港创建新用户。 注意：当auth\_mode设置为ldap\_auth时，自注册功能始终被禁用，并且该标志被忽略。

* token\_expiration：令牌服务创建的令牌的到期时间（以分钟为单位），默认值为30分钟。

* project\_creation\_restriction：用于控制用户有权创建项目的标志。默认情况下，每个人都可以创建一个项目，设置为“adminonly”，以便只有admin才能创建项目。

* verify\_remote\_cert：（上或关闭，默认为上）该标志，判断是否验证SSL / TLS证书时码头与远程注册表实例通信。将此属性设置为off可绕过SSL / TLS验证，SSL / TLS验证通常在远程实例具有自签名或不受信任的证书时使用。

#### 配置存储后端（可选）

默认情况下，Harbor将映像存储在本地文件系统上。在生产环境中，您可以考虑使用其他存储后端而不是本地文件系统，如S3，Openstack Swift，Ceph等。您需要更新的是`storage`文件中的部分`common/templates/registry/config.yml`。例如，如果您使用Openstack Swift作为存储后端，则该部分可能如下所示：

```
storage:
  swift:
    username: admin
    password: ADMIN_PASS
    authurl: http://keystone_addr:35357/v3/auth
    tenant: admin
    domain: default
    region: regionOne
    container: docker_images
```

注意：有关注册表的存储后端的详细信息，请参阅[registry配置参考](https://docs.docker.com/registry/configuration/)。

#### 完成安装和启动港

一旦配置了ports.cfg和存储后端（可选），请使用`install.sh`脚本安装并启动Harbor 。请注意，在线安装程序可能需要一些时间才能从Docker集线器下载Harbour图像。

##### 默认安装（无公证）

在1.1.0版本之后，Harbor已经与Notary进行了集成，但默认情况下安装不包括公证服务。

```
./install.sh
```

如果一切正常，您应该可以打开一个浏览器，访问[http://reg.yourdomain.com](http://reg.yourdomain.com/)的管理员门户（将reg.yourdomain.com更改为您配置的主机名`harbor.cfg`）。请注意，默认管理员用户名/密码为admin / Harbor12345。

登录管理员门户并创建一个新的项目，例如`myproject`。然后，您可以使用docker命令登录和推送图像（默认情况下，注册表服务器侦听端口80）：

```
docker login reg.yourdomain.com
docker push reg.yourdomain.com/myproject/myrepo:mytag
```

重要信息： Harbor的默认安装使用HTTP – 因此，您需要将该选项添加`--insecure-registry`到客户端的Docker守护程序中，然后重新启动Docker服务。

##### 公证人安装

要使用公证服务安装Harbor，请在运行时添加参数`install.sh`：

```
./install.sh --with-notary
```

注意：对于使用公证人员进行安装，必须将参数ui\_url\_protocol设置为“https”。配置HTTPS请参考以下章节。

有关Notary和Docker Content Trust的更多信息，请参阅Docker的文档：[https:](https://docs.docker.com/engine/security/trust/content_trust/)[//docs.docker.com/engine/security/trust/content\_trust/](https://docs.docker.com/engine/security/trust/content_trust/)

有关如何使用Harbor 的信息，请参阅[Harbor 用户指南](https://github.com/vmware/harbor/blob/master/docs/user_guide.md)。

#### 使用HTTPS访问配置港口

Harbor 不附带任何证书，默认情况下，使用HTTP提供请求。虽然这使得设置和运行相对简单 – 特别是对于开发或测试环境 – 不建议在生产环境中使用。要启用HTTPS，请参阅[使用HTTPS访问配置Harbor ](https://github.com/vmware/harbor/blob/master/docs/configure_https.md)。

### 管理港口的生命周期

您可以使用docker-compose来管理Harbor的生命周期。一些有用的命令如下所列（必须与docker -compose.yml在同一目录中运行）。

停止Harbor ：

```
docker-compose stop
```

停车后重新启动Harbor ：

```
docker-compose star
```

要更改Harbor的配置，请首先停止现有的Harbor实例并进行更新`harbor.cfg`。然后运行`prepare`脚本来填充配置。最后重新创建并启动Harbor的实例：

```
docker-compose down -v
vim harbor.cfg
prepare
docker-compose up -d
```

删除Harbor 的容器，同时保留图像数据和Harbor的数据库文件在文件系统上：

```
docker-compose down -v
```

删除Harbor 的数据库和图像数据（为了干净的重新安装）：

```
rm -r / data / database
rm -r / data / registry
```

#### 管理港湾的安全生产周期

当Harbor 与Notary安装时，docker `docker-compose.notary.yml`-compose命令需要一个额外的模板文件。用于管理Harbor 生命周期的码头组合命令是：

```
docker-compose -f ./docker-compose.yml -f ./docker-compose.notary.yml [ up|down|ps|stop|start ]
```

例如，如果要在配有Notary的情况下更改配置`harbor.cfg`并重新部署Harbor，则应使用以下命令：

```
docker-compose -f ./docker-compose.yml -f ./docker-compose.notary.yml down -v
vim harbor.cfg
准备 - 公证
docker-compose -f ./docker-compose.yml -f ./docker-compose.notary.yml up -d
```

有关[docker](https://docs.docker.com/compose/reference/)-compose的更多信息，请查看[Docker Compose命令行参考](https://docs.docker.com/compose/reference/)。

### 持久性数据和日志文件

默认情况下，注册表数据将保留在主机的`/data/`目录中。即使拆除和/或重建Harbor 的集装箱，这些数据也保持不变。

另外，Harbor使用rsyslog收集每个容器的日志。默认情况下，这些日志文件存储在`/var/log/harbor/`目标主机上的目录中进行故障排除。

## 配置在自定义端口上监听港口

默认情况下，对于admin portal和docker命令，Harbor将在端口80（HTTP）和443（如果已配置HTTPS）上进行监听，则可以使用自定义方式进行配置。

### 对于HTTP协议

1.修改docker-compose.yml  
将第一个“80”替换为自定义的端口，例如8888：80。

```
proxy:
    image: library/nginx:1.11.5
    restart: always
    volumes:
      - ./config/nginx:/etc/nginx
    ports:
      - 8888:80
      - 443:443
    depends_on:
      - mysql
      - registry
      - ui
      - log
    logging:
      driver: "syslog"
      options:  
        syslog-address: "tcp://127.0.0.1:1514"
        tag: "proxy"
```

2.修改ports.cfg，将端口添加到参数“hostname”

```
hostname = 192.168.0.2:8888
```

3.重新部署港口参考上一节“管理Harbor 的生命周期”。

### 对于HTTPS协议

按照本[指南](https://github.com/vmware/harbor/blob/master/docs/configure_https.md)，启用Harbor 中的HTTPS 。  
2.修改docker-compose.yml  
将第一个“443”替换为自定义的端口，例如8888：443。

```
proxy:
    image: library/nginx:1.11.5
    restart: always
    volumes:
      - ./config/nginx:/etc/nginx
    ports:
      - 80:80
      - 8888:443
    depends_on:
      - mysql
      - registry
      - ui
      - log
    logging:
      driver: "syslog"
      options:  
        syslog-address: "tcp://127.0.0.1:1514"
        tag: "proxy"
```

3.修改ports.cfg，将端口添加到参数“hostname”

```
hostname = 192.168.0.2:8888
```

4.重新部署Harbor 参考上一节“管理Harbor 的生命周期”。

## 故障排除

1. 当Harbour不能正常工作时，请运行以下命令，查看Harbor的所有容器是否处于UP状态：

```
    $ sudo docker-compose ps
        Name                     Command               State                    Ports                   
  -----------------------------------------------------------------------------------------------------
  harbor-db           docker-entrypoint.sh mysqld      Up      3306/tcp                                 
  harbor-jobservice   /harbor/harbor_jobservice        Up                                               
  harbor-log          /bin/sh -c crond && rsyslo ...   Up      127.0.0.1:1514->514/tcp                    
  harbor-ui           /harbor/harbor_ui                Up                                               
  nginx               nginx -g daemon off;             Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp 
  registry            /entrypoint.sh serve /etc/ ...   Up      5000/tcp
```

如果容器未处于UP状态，请检查目录中该容器的日志文件`/var/log/harbor`。例如，如果容器`harbor-ui`没有运行，则应该查看日志文件`ui.log`。

2，当设置Harbor 后面的nginx的代理或弹性负载均衡，寻找线下，在`common/templates/nginx/nginx.http.conf`从部分删除它，如果代理已经有类似的设置：`location /`，`location /v2/`和`location /service/`。

```
proxy_set_header X-Forwarded-Proto $scheme;
```

并重新部署Harbor 参考上一节“管理Harbor 的生命周期”。

