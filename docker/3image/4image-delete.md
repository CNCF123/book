## 删除镜像 {#删除本地镜像}

删除本地的镜像，使用`docker image rm`命令，其格式为：

```
# docker image rm [选项] <镜像1> [<镜像2> ...]
```

* **docker image rm** 等同于 **docker rmi**

### 用 ID、镜像名、摘要删除镜像 {#用-id、镜像名、摘要删除镜像}

其中，`<镜像>`可以是`镜像短 ID`、`镜像长 ID`、`镜像名`或者`镜像摘要`。

比如我们有这么一些镜像：

```
# docker image ls
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
centos                      latest              0584b3d2cf6d        3 weeks ago         196.5 MB
redis                       alpine              501ad78535f0        3 weeks ago         21.03 MB
docker                      latest              cf693ec9b5c7        3 weeks ago         105.1 MB
nginx                       latest              e43d811ce2f4        5 weeks ago         181.5 MB
```

我们可以用镜像的完整 ID，也称为`长 ID`，来删除镜像。使用脚本的时候可能会用长 ID，但是人工输入就太累了，所以更多的时候是用`短 ID`来删除镜像。`docker image ls`默认列出的就已经是短 ID 了，一般取前3个字符以上，只要足够区分于别的镜像就可以了。

比如这里，如果我们要删除`redis:alpine`镜像，可以执行：

```
# docker image rm 501
Untagged: redis:alpine
Untagged: redis@sha256:f1ed3708f538b537eb9c2a7dd50dc90a706f7debd7e1196c9264edeea521a86d
Deleted: sha256:501ad78535f015d88872e13fa87a828425117e3d28075d0c117932b05bf189b7
Deleted: sha256:96167737e29ca8e9d74982ef2a0dda76ed7b430da55e321c071f0dbff8c2899b
Deleted: sha256:32770d1dcf835f192cafd6b9263b7b597a1778a403a109e2cc2ee866f74adf23
Deleted: sha256:127227698ad74a5846ff5153475e03439d96d4b1c7f2a449c7a826ef74a2d2fa
Deleted: sha256:1333ecc582459bac54e1437335c0816bc17634e131ea0cc48daa27d32c75eab3
Deleted: sha256:4
fc
455b921edf9c4aea207c51ab39b10b06540c8b4825ba57b3feed1668fa7c7
```

我们也可以用`镜像名`，也就是`<仓库名>:<标签>`，来删除镜像。

```
# docker image rm centos
Untagged: centos:latest
Untagged: centos@sha256:b2f9d1c0ff5f87a4743104d099a3d561002ac500db1b9bfa02a783a46e0d366c
Deleted: sha256:0584b3d2cf6d235ee310cf14b54667d889887b838d3f3d3033acd70
fc
3c48b8a
Deleted: sha256:97ca462ad9eeae25941546209454496e1d66749d53dfa2ee32bf1faabd239d38
```

当然，更精确的是使用`镜像摘要`docker image ls --digests，删除镜像。

```
# docker image ls --digests
REPOSITORY                  TAG                 DIGEST                                                                    IMAGE ID            CREATED             SIZE
node                        slim                sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228   6e0c4c8e3913        3 weeks ago         214 MB

# docker image rm node@sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228
Untagged: node@sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228
```

### 用 docker image ls 命令来配合 {#用-docker-image-ls-命令来配合}

像其它可以承接多个实体的命令一样，可以使用`docker image ls -q`来配合使用`docker image rm`，这样可以成批的删除希望删除的镜像。我们在“镜像列表”章节介绍过很多过滤镜像列表的方式都可以拿过来使用。

比如，我们需要删除所有仓库名为`redis`的镜像：

```
# docker image rm $(docker image ls -q redis)
```

### 清理镜像

一段时间后， 系统中可能会遗留一些临时的镜像文件， 以及一些没有被使用的镜像， 可以通过docker image prune命令来进行清理

-a, -all: 删除所有无用镜像， 不光是临时镜像; 

-filter: 只清理符合给定过滤器的镜像; 

-f, -force: 强制删除镜像。





