# 前提条件

* 您需要准备一个可用的镜像仓库。本例中使用阿里云容器镜像服务，在上面构建了一个可用的仓库。
* 设置仓库的独立登录密码，本示例中使用阿里云容器镜像服务，在
  [容器镜像服务控制台](https://cr.console.aliyun.com/)
  上设置与修改 Registry 的登录密码。注意首次修改 Registry 登录密码即是设置密码。

## 背景信息 {#h2-url-1}

您可以在目标集群上登录到镜像仓库，从而提供相关的集群登录信息，方便您使用集群管理工具进行管理。

## 操作步骤 {#h2-url-2}

1. 登录
   [容器服务管理控制台](https://cs.console.aliyun.com/)
   。
2. 在 Swarm 菜单下，单击左侧导航栏中的
   集群
   。
3. 选择要配置的集群并单击
   管理
   。
 
   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7003/4833_zh-CN.png)
 
4. 在集群的详情页面，单击
   登录镜像仓库
   。
 
   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7003/4834_zh-CN.png)
 
5. 在弹出的对话框中，对相关参数进行配置。
 
   ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7003/4835_zh-CN.png)
 
   * 仓库域名：镜像仓库的 hub 域名。以一个镜像地址
     `registry.cn-hangzhou.aliyuncs.com/acs/agent:0.8`
     为例，
     `registry.cn-hangzhou.aliyuncs.com`
     即是仓库域名。
   * 用户名：您当前登录的阿里云账号全称。
   * 密码：Registry 的独立登录密码。Registry 的登录密码是在容器镜像服务的控制台上设置与修改的。
   * 邮箱：可选。
6. 单击
   确定
   后，若无报错信息，说明已经成功登录到镜像仓库。



