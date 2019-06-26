## Harbor

几个VMware中国的研发人员搞了一个开源容器镜像仓库。简单来说，容器是集装箱，集装箱放哪里呢？对，harbor港湾！

官方的说法是：Harbor是一个用于存储和分发Docker镜像的企业级Registry服务器。

#### Harbor的架构

harbor的整体架构还是很清晰的，下面简单介绍一下，下图展示harbor主要的功能组件和信息流向。

主要组件包括proxy，他是一个nginx前端代理，主要是分发前端页面ui访问和镜像上传和下载流量，上图中通过深蓝色先标识；ui提供了一个web管理页面，当然还包括了一个前端页面和后端API，底层使用mysql数据库；registry是镜像仓库，负责存储镜像文件，当镜像上传完毕后通过hook通知ui创建repository，上图通过红色线标识，当然registry的token认证也是通过ui组件完成；adminserver是系统的配置管理中心附带检查存储用量，ui和jobserver启动时候回需要加载adminserver的配置，通过灰色线标识；jobsevice是负责镜像复制工作的，他和registry通信，从一个registry pull镜像然后push到另一个registry，并记录job\_log，上图通过紫色线标识；log是日志汇总组件，通过docker的log-driver把日志汇总到一起，通过浅蓝色线条标识。

#### Harbor使用

1.用户管理

基于角色的访问控制，RBAC，这个是k8s 1.6以后才加入的功能，harbor在设计的开始就考虑进去，用户分为三种角色：项目管理员（MDRWS）、开发人员（RWS）和访客（RS），当然还有一个最高管理员权限admin系统管理员。

上面的简称大概说一下，M:管理、D:删除、R:读取、W:写入、S:查询，非常细致的权限管理体系。当然一个用户可以在不同的项目里面扮演不同角色，这个和现实的用户管理体系非常吻合。

2.项目管理

项目管理是系统最主要的一个功能模块，项目是一组镜像仓库的逻辑集合，是权限管理和资源管理的单元划分。一个项目下面有多个镜像仓库，并且关联多个不同角色的成员，镜像复制也是基于项目的，通过添加复制规则，可以将项目下面的镜像从一个harbor迁移到另一个harbor，并且可以通过日志查看复制过程，并有retry机制。

3.配置管理和日志查询

配置管理主要是配置harbor的认证模式，企业内部使用，通常都是对接到公司LDAP上面，当然harbor也支持数据库认证；还可以设置token的有效时间。用户对镜像的pull和push操作都可以被harbor记录下来，这样为排查文件提供了重要手段。

当然harbor功能不止我上面说的，譬如harbor集成了clair镜像扫描功能，它是cereos开发的一款漏洞扫描工具，可以检查镜像操作系统以及上面安装包是否与已知不安全的包版本相匹配，从而提高镜像安全性。

#### Harbor和kubernetes结合

kubernetes是什么我就不在此赘述了，简单一句话，它是一个用于自动部署，扩展和管理容器化应用程序的开源系统。它的详细内容参考请参考官网。kubernetes中的kubelet组件负责容器的生命周期管理，通过CRI对接到不同的容器实现如：rkt、Docker等，目前默认是Docker，kubelet启动Docker容器的时候，会根据策略IfNotPresent是如果本地不存在则拉取，打上latest标签或者设置Always策略则是一直拉取最新镜像。我们在当前的企业中，Docker的镜像仓库配置成harbor，在容器启动是会拉取harbor中的镜像。这里又几个问题需要注意

第一，imagePullSecrets

镜像拉取需要先登录，在kubernetes多用户使用场景中，镜像的安全必须要保证，一个用户是没有权限拉取另一个用户下面的镜像。kubernetes提供了imagePullSecrets负责管理镜像拉取时的安全认证。其实kubelet就是模拟了一次用户登录，并且会在~/.docker/config.json下面生成auth认证信息。当然如果是绝对单个用户的场景，为了避免麻烦，你也是使用admin到每台机器上面登录（docker login）一下，这样镜像拉取就都是通过admin来完成。

第二，https

为了安全建议使用https，我们在生成环境中使用的是自签名的证书，如果是设置成https，需要在设置非安全仓库–insecure-registry，并重启Docker服务。

反过来，harbor本身的部署也可以通过kubernetes来完成，通过kubernetes的rc和svc以及pv的配合完成harbor的创建。目前我们的生成环境还是沿用docker-compose的方式部署。





