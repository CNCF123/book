## VOLUME

```
VOLUME ["/data"]
```

该`VOLUME`指令创建具有指定名称的安装点，并将其标记为从本机主机或其他容器保存外部安装的卷。该值可以是JSON数组，`VOLUME ["/var/log/"]`或具有多个参数的普通字符串，例如`VOLUME /var/log`或`VOLUME /var/log /var/db`。有关通过Docker客户端提供的更多信息/示例和安装说明，请参阅[_通过卷共享目录_](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-host-directory-as-a-data-volume)文档。

该`docker run`命令使用基础映像中指定位置存在的任何数据初始化新创建的卷。例如，请考虑以下Dockerfile片段：

```
FROM ubuntu

RUN mkdir /myvol

RUN echo "hello world" 
>
 /myvol/greeting

VOLUME /myvol
```

此Dockerfile会`docker run`生成一个图像，该图像将导致创建新的挂载点`/myvol`并将`greeting`文件复制到新创建的卷中。

### 有关指定卷的说明 {#notes-about-specifying-volumes}

关于卷中的卷，请记住以下事项`Dockerfile`。

* **基于Windows的容器上的卷**：使用基于Windows的容器时，容器中卷的目标必须是以下之一：

  * 不存在或空目录
  * 除了之外的驱动器
    `C:`

* **从Dockerfile中更改卷**：如果任何构建步骤在声明后**更改卷内的**数据，那么这些更改将被丢弃。

* **JSON格式**：列表被解析为JSON数组。您必须用双引号（`"`）而不是单引号（`'`）括起单词。

* **主机目录在容器运行时声明**：主机目录（mountpoint）本质上是依赖于主机的。这是为了保持图像的可移植性，因为不能保证给定的主机目录在所有主机上都可用。因此，您无法从Dockerfile中安装主机目录。该`VOLUME`指令不支持指定`host-dir`参数。您必须在创建或运行容器时指定安装点。

  




