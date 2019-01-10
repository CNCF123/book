## EXPOSE {#expose}

```
EXPOSE <port> [<port>/<protocol>...]
```

该`EXPOSE`指令通知Docker容器在运行时侦听指定的网络端口。您可以指定端口是侦听TCP还是UDP，如果未指定协议，则默认为TCP。

该`EXPOSE`指令实际上没有发布端口。它作为构建映像的人和运行容器的人之间的一种文档，用于发布要发布的端口。要在运行容器时实际发布端口，请使用`-p`标志on`docker run`来发布和映射一个或多个端口，或使用`-P`标志发布所有公开的端口并将它们映射到高阶端口。

默认情况下，`EXPOSE`假定为TCP。您还可以指定UDP：

```
EXPOSE 80/udp

```

要在TCP和UDP上公开，请包含两行：

```
EXPOSE 80/tcp

EXPOSE 80/udp
```

在这种情况下，如果使用`-P`with`docker run`，端口将为TCP暴露一次，对UDP则暴露一次。请记住，`-P`在主机上使用短暂的高阶主机端口，因此TCP和UDP的端口不同。

无论`EXPOSE`设置如何，您都可以使用`-p`标志在运行时覆盖它们。例如

```
docker run 
-p
 80:80/tcp 
-p
 80:80/udp ...

```

要在主机系统上设置端口重定向，请参阅[使用-P标志](https://docs.docker.com/engine/reference/run/#expose-incoming-ports)。该`docker network`命令支持为容器之间的通信创建网络，而无需公开或发布特定端口，因为连接到网络的容器可以通过任何端口相互通信。有关详细信息，请参阅[此功能](https://docs.docker.com/engine/userguide/networking/)的[概述](https://docs.docker.com/engine/userguide/networking/)）。

  


