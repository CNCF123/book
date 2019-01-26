## 镜像列表 {#列出镜像}

列出已经下载下来的镜像，使用`docker image ls`命令。

```
docker image ls
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
redis               latest              82629e941a38        3 days ago          95MB
nginx               latest              42b4762643dc        3 days ago          109MB
ubuntu              16.04               7e87e2b3bf7a        3 days ago          117MB
ubuntu              18.04               20bb25d32758        3 days ago          87.5MB
ubuntu              latest              20bb25d32758        3 days ago          87.5MB
hello-world         latest              fce289e99eb9        3 weeks ago         1.84kB
busybox             latest              3a093384ac30        3 weeks ago         1.2MB
nginx               alpine              315798907716        4 weeks ago         17.8MB
<none>              <none>              00285df0df87        5 days ago          342 MB
```

列表包含了`仓库名`、`标签`、`镜像 ID`、`创建时间`以及`所占用的空间`。

其中仓库名、标签在之前的基础概念章节已经介绍过了。**镜像 ID**则是镜像的唯一标识，一个镜像可以对应多个**标签**。

在上面的例子中，我们可以看到ubuntu:**18.04**和ubuntu:**latest**拥有相同的 ID（20bb25d32758），它们对应的是同一个镜像。

### 镜像体积 {#镜像体积}

如果仔细观察，会注意到这里标识的所占用空间和在 Docker Hub 上看到的镜像大小不同。

比如，`ubuntu:16.04`镜像大小，在这里是`127 MB`，但是在[Docker Hub](https://hub.docker.com/r/library/ubuntu/tags/)显示的却是`50 MB`。

这是因为 Docker Hub 中显示的体积是**压缩后的体积**。

在镜像下载和上传过程中镜像是保持着压缩状态的，因此 Docker Hub 所显示的大小是网络传输中更关心的流量大小。而`docker image ls`显示的是镜像下载到本地后，**展开的大小**，准确说，是展开后的各层所占空间的总和，因为镜像到本地后，查看空间的时候，更关心的是本地磁盘空间占用的大小。

另外一个需要注意的问题是，`docker image ls`列表中的镜像体积总和并非是所有镜像实际硬盘消耗。由于 Docker 镜像是多层存储结构，并且可以继承、复用，因此不同镜像可能会因为使用相同的基础镜像，从而拥有共同的层。由于 Docker 使用 Union FS，相同的层只需要保存一份即可，因此实际镜像硬盘占用空间很可能要比这个列表镜像大小的总和要小的多。

你可以通过以下命令来便捷的查看镜像、容器、数据卷所占用的空间。

```
$ docker system df

TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              24                  0                   1.992GB             1.992GB (100%)
Containers          1                   0                   62.82MB             62.82MB (100%)
Local Volumes       9                   0                   652.2MB             652.2MB (100%)
Build Cache                                                 0B                  0B
```

### 虚悬镜像 {#虚悬镜像}

上面的镜像列表中，还可以看到一个特殊的镜像，这个镜像既**没有仓库名，也没有标签**，均为`<none>`。：

```
<none>           <none>                  00285df0df87        5 days ago          342 MB
```

这个镜像原本是有镜像名和标签的，原来为`mongo:3.2`，随着官方镜像维护，发布了新版本后，重新`docker pull mongo:3.2`时，`mongo:3.2`这个镜像名被转移到了新下载的镜像身上，而旧的镜像上的这个名称则被取消，从而成为了`<none>`。除了`docker pull`可能导致这种情况，`docker build`也同样可以导致这种现象。**由于新旧镜像同名，旧镜像名称被取消**，从而出现仓库名、标签均为`<none>`的镜像。这类无标签镜像也被称为**虚悬镜像\(dangling image\)**，可以用下面的命令专门显示这类镜像：

```
docker image ls -f dangling=true

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

<none>             <none>               00285df0df87        5 days ago          342 MB
```

一般来说，虚悬镜像已经失去了存在的价值，是**可以随意删除**的，可以用下面的命令删除。

```
docker image prune
```

### 中间层镜像 {#中间层镜像}

为了加速镜像构建、重复利用资源，Docker 会利用**中间层镜像**。所以在使用一段时间后，可能会看到一些依赖的中间层镜像。默认的`docker image ls`列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加`-a`参数。

```
docker image ls -a
```

这样会看到很多无标签的镜像，与之前的虚悬镜像不同，这些无标签的镜像很多都是中间层镜像，是其它镜像所依赖的镜像。这些无标签镜像不应该删除，否则会导致上层镜像因为依赖丢失而出错。实际上，这些镜像也没必要删除，因为之前说过，相同的层只会存一遍，而这些镜像是别的镜像的依赖，因此并不会因为它们被列出来而多存了一份，无论如何你也会需要它们。只要删除那些依赖它们的镜像后，这些依赖的中间层镜像也会被连带删除。

### 列出部分镜像 {#列出部分镜像}

不加任何参数的情况下，`docker image ls`会列出所有顶级镜像，但是有时候我们只希望列出部分镜像。`docker image ls`有好几个参数可以帮助做到这个事情。

根据仓库名列出镜像

```
docker image ls ubuntu
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              16.04               7e87e2b3bf7a        3 days ago          117MB
ubuntu              18.04               20bb25d32758        3 days ago          87.5MB
ubuntu              latest              20bb25d32758        3 days ago          87.5MB
```

列出特定的某个镜像，也就是说指定仓库名和标签

```
docker image ls ubuntu:16.04
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              16.04               f753707788c5        4 weeks ago         127 MB
```

除此以外，`docker image ls`还支持强大的过滤器参数`--filter`，或者简写`-f`。之前我们已经看到了使用过滤器来列出虚悬镜像的用法，它还有更多的用法。比如，我们希望看到在`mongo:3.2`之后建立的镜像，可以用下面的命令：

```
docker image ls -f since=mongo:3.2
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
redis               latest              5f515359c7f8        5 days ago          183 MB
nginx               latest              05a60462f8ba        5 days ago          181 MB
```

想查看某个位置之前的镜像也可以，只需要把`since`换成`before`即可。

此外，如果镜像构建时，定义了`LABEL`，还可以通过`LABEL`来过滤。

```

```

### 以特定格式显示 {#以特定格式显示}

默认情况下，`docker image ls`会输出一个完整的表格，但是我们并非所有时候都会需要这些内容。比如，刚才删除虚悬镜像的时候，我们需要利用`docker image ls`把所有的虚悬镜像的 ID 列出来，然后才可以交给`docker image rm`命令作为参数来删除指定的这些镜像，这个时候就用到了`-q`参数。

```
docker image ls -q
5f515359c7f8
05a60462f8ba
fe9198c04d62
00285df0df87
f753707788c5
f753707788c5
1e0c3dd64ccd
```



