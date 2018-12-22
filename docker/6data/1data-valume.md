## 数据卷 {#数据卷}

数据卷是一个可供一个或多个容器使用的特殊目录，可以提供很多有用的特性：

* 数据卷可以在容器之间共享和重用

* 对数据卷的修改会立马生效

* 对数据卷的更新，不会影响镜像

* 数据卷默认会一直存在，即使容器被删除

> 注意：数据卷的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会隐藏掉，能显示看的是挂载的数据卷。

#### 创建一个数据卷

```
docker volume create my-volume
```

查看所有的数据卷

```
docker volume ls 

local               my-volume
```

查看指定数据卷的信息

```
docker volume inspect my-volume
[
    {
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-volume/_data",
        "Name": "my-volume",
        "Options": {},
        "Scope": "local"
    }
]
```

### 启动一个挂载数据卷的容器 {#启动一个挂载数据卷的容器}

在用docker run命令的时候，使用--mount标记来将数据卷挂载到容器里。在一次docker run中可以挂载多个数据卷。

下面创建一个名为web的容器，并加载一个数据卷到容器的/webapp目录。

```
docker run -d -P --name web  -v my-volume:/wepapp --mount source=my-volume,target=/webapp training/webapp python app.py
```

### 查看数据卷的具体信息 {#查看数据卷的具体信息}

在主机里使用以下命令可以查看web容器的信息

```
docker inspect web
```

数据卷信息在 "Mounts" Key 下面

```
"Mounts": [
    {
        "Type": "volume",
        "Name": "my-volume",
        "Source": "/var/lib/docker/volumes/my-volume/_data",
        "Destination": "/app",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
```

### 删除数据卷 {#删除数据卷}

```
docker volume rm my-volume
```

数据卷是被设计用来持久化数据的，它的生命周期独立于容器，Docker 不会在容器被删除后自动删除数据卷，并且也不存在垃圾回收这样的机制来处理没有任何容器引用的数据卷。如果需要在删除容器的同时移除数据卷。可以在删除容器的时候使用docker rm -v这个命令。

无主的数据卷可能会占据很多空间，要清理请使用以下命令

```
docker volume prune
```



