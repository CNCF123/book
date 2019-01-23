## Dockerfile 创建镜像

从刚才的`docker commit`的学习中，我们可以了解到，镜像的定制实际上就是定制每一层所添加的配置、文件。如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题就都会解决。这个脚本就是 Dockerfile。

Dockerfile 是一个文本文件，其内包含了一条条的**指令\(Instruction\)**，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

还以之前定制`nginx`镜像为例，这次我们使用 Dockerfile 来定制。

在一个空白目录中，建立一个文本文件，并命名为`Dockerfile`：

```
$ mkdir mynginx
$ cd mynginx
$ touch Dockerfile
```

其内容为：

```
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

这个 Dockerfile 很简单，一共就两行。涉及到了两条指令，`FROM`和`RUN`。

### 构建镜像 {#from-指定基础镜像}

好了，让我们再回到之前定制的 nginx 镜像的 Dockerfile 来。现在我们明白了这个 Dockerfile 的内容，那么让我们来构建这个镜像吧。

在`Dockerfile`文件所在目录执行：

```
$ docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM nginx
 ---> e43d811ce2f4
Step 2 : RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
 ---> Running in 9cdc27646c7b
 ---> 44aa4490ce2c
Removing intermediate container 9cdc27646c7b
Successfully built 44aa4490ce2c
```

从命令的输出结果中，我们可以清晰的看到镜像的构建过程。在`Step 2`中，如同我们之前所说的那样，`RUN`指令启动了一个容器`9cdc27646c7b`，执行了所要求的命令，并最后提交了这一层`44aa4490ce2c`，随后删除了所用到的这个容器`9cdc27646c7b`。

这里我们使用了`docker build`命令进行镜像构建。其格式为：

```
docker build [选项] <上下文路径/URL/->
```

在这里我们指定了最终镜像的名称`-t nginx:v3`，构建成功后，我们可以像之前运行`nginx:v2`那样来运行这个镜像，其结果会和`nginx:v2`一样。

### 镜像构建上下文（Context） {#镜像构建上下文（context）}

如果注意，会看到`docker build`命令最后有一个`.`。`.`表示当前目录，而`Dockerfile`就在当前目录，因此不少初学者以为这个路径是在指定`Dockerfile`所在路径，这么理解其实是不准确的。如果对应上面的命令格式，你可能会发现，这是在指定**上下文路径**。那么什么是上下文呢？

首先我们要理解`docker build`的工作原理。Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具。Docker 的引擎提供了一组 REST API，被称为[Docker Remote API](https://docs.docker.com/engine/reference/api/docker_remote_api/)，而如`docker`命令这样的客户端工具，则是通过这组 API 与 Docker 引擎交互，从而完成各种功能。因此，虽然表面上我们好像是在本机执行各种`docker`功能，但实际上，一切都是使用的远程调用形式在服务端（Docker 引擎）完成。也因为这种 C/S 设计，让我们操作远程服务器的 Docker 引擎变得轻而易举。

当我们进行镜像构建的时候，并非所有定制都会通过`RUN`指令完成，经常会需要将一些本地文件复制进镜像，比如通过`COPY`指令、`ADD`指令等。而`docker build`命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎中构建的。那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢？

这就引入了上下文的概念。当构建的时候，用户会指定构建镜像上下文的路径，`docker build`命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

如果在`Dockerfile`中这么写：

```
COPY ./package.json /app/
```

这并不是要复制执行`docker build`命令所在的目录下的`package.json`，也不是复制`Dockerfile`所在目录下的`package.json`，而是复制**上下文（context）**目录下的`package.json`。

因此，`COPY`这类指令中的源文件的路径都是_相对路径_。这也是初学者经常会问的为什么`COPY ../package.json /app`或者`COPY /opt/xxxx /app`无法工作的原因，因为这些路径已经超出了上下文的范围，Docker 引擎无法获得这些位置的文件。如果真的需要那些文件，应该将它们复制到上下文目录中去。

现在就可以理解刚才的命令`docker build -t nginx:v3 .`中的这个`.`，实际上是在指定上下文的目录，`docker build`命令会将该目录下的内容打包交给 Docker 引擎以帮助构建镜像。

如果观察`docker build`输出，我们其实已经看到了这个发送上下文的过程：

```
$ docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB
...
```

理解构建上下文对于镜像构建是很重要的，避免犯一些不应该的错误。比如有些初学者在发现`COPY /opt/xxxx /app`不工作后，于是干脆将`Dockerfile`放到了硬盘根目录去构建，结果发现`docker build`执行后，在发送一个几十 GB 的东西，极为缓慢而且很容易构建失败。那是因为这种做法是在让`docker build`打包整个硬盘，这显然是使用错误。

一般来说，应该会将`Dockerfile`置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用`.gitignore`一样的语法写一个`.dockerignore`，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的。

那么为什么会有人误以为`.`是指定`Dockerfile`所在目录呢？这是因为在默认情况下，如果不额外指定`Dockerfile`的话，会将上下文目录下的名为`Dockerfile`的文件作为 Dockerfile。

这只是默认行为，实际上`Dockerfile`的文件名并不要求必须为`Dockerfile`，而且并不要求必须位于上下文目录中，比如可以用`-f ../Dockerfile.php`参数指定某个文件作为`Dockerfile`。

当然，一般大家习惯性的会使用默认的文件名`Dockerfile`，以及会将其置于镜像构建上下文目录中。

### 其它`docker build`的用法 {#其它-docker-build-的用法}

#### 直接用 Git repo 进行构建 {#直接用-git-repo-进行构建}

或许你已经注意到了，`docker build`还支持从 URL 构建，比如可以直接从 Git repo 中构建：

```
$ docker build https://github.com/twang2218/gitlab-ce-zh.git#:8.14

docker build https://github.com/twang2218/gitlab-ce-zh.git\#:8.14

Sending build context to Docker daemon 2.048 kB
Step 1 : FROM gitlab/gitlab-ce:8.14.0-ce.0
8.14.0-ce.0: Pulling from gitlab/gitlab-ce
aed15891ba52: Already exists
773ae8583d14: Already exists
...
```

这行命令指定了构建所需的 Git repo，并且指定默认的`master`分支，构建目录为`/8.14/`，然后 Docker 就会自己去`git clone`这个项目、切换到指定分支、并进入到指定目录后开始构建。

#### 用给定的 tar 压缩包构建 {#用给定的-tar-压缩包构建}

```
$ docker build http://server/context.tar.gz
```

如果所给出的 URL 不是个 Git repo，而是个`tar`压缩包，那么 Docker 引擎会下载这个包，并自动解压缩，以其作为上下文，开始构建。

#### 从标准输入中读取 Dockerfile 进行构建 {#从标准输入中读取-dockerfile-进行构建}

```
docker build - < Dockerfile
```

或

```
cat Dockerfile | docker build -
```

如果标准输入传入的是文本文件，则将其视为`Dockerfile`，并开始构建。这种形式由于直接从标准输入中读取 Dockerfile 的内容，它没有上下文，因此不可以像其他方法那样可以将本地文件`COPY`进镜像之类的事情。

#### 从标准输入中读取上下文压缩包进行构建 {#从标准输入中读取上下文压缩包进行构建}

```
$ docker build - < context.tar.gz
```

如果发现标准输入的文件格式是`gzip`、`bzip2`以及`xz`的话，将会使其为上下文压缩包，直接将其展开，将里面视为上下文，并开始构建。

