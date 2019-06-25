## 启动容器 {#启动容器}

启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（`stopped`）的容器重新启动。

因为 Docker 的容器实在太轻量级了，很多时候用户都是随时删除和新创建容器。

### 新建并启动

所需要的命令主要为**docker run**。

例如，下面的命令输出一个 “Hello World”，之后终止容器。

```
docker run ubuntu:16.04 /bin/echo 'Hello world'

Hello world
```

这跟在本地直接执行`/bin/echo 'hello world'`几乎感觉不出任何区别。

### 交互模式

下面的命令则启动一个 bash 终端，允许用户进行交互。

```
docker run -it ubuntu:16.04 /bin/bash
root@af8bae53bdd3:/#
```

使用-it子命令，`-t`选项让Docker分配一个伪终端（tty）并绑定到容器的标准输入上，`-i`则让容器的标准输入保持打开。

在交互模式下，用户可以通过所创建的终端来输入命令，例如

```
root@af8bae53bdd3:/# pwd
/

root@af8bae53bdd3:/# ls
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
```

### 日志信息

要获取容器的输出信息，可以通过`docker logs`命令。

```
$ docker logs [container ID or NAMES]
hello world
hello world
hello world
. . .
```

### 重新启动已终止的容器 {#启动已终止容器}

可以利用`docker container start`命令，直接将一个已经终止的容器重新启动运行。

容器的核心为所执行的应用程序，所需要的资源都是应用程序运行所必需的。除此之外，并没有其它的资源。可以在伪终端中利用`ps`或`top`来查看进程信息。

```
root@ba267838cc1b:/# ps

  PID TTY          TIME CMD
    1 ?        00:00:00 bash
   11 ?        00:00:00 ps
```

可见，容器中仅运行了指定的 bash 应用。这种特点使得 Docker 对资源的利用率极高，是货真价实的轻量级虚拟化。

### 重新启动正在运行的容器 {#启动已终止容器}

`docker container restart`命令会将一个运行态的容器终止，然后再重新启动它。

## 

## 终止容器

可以使用`docker container stop`来终止一个运行中的容器。

此外，当 Docker 容器中指定的应用终结时，容器也自动终止。

例如对于上一章节中只启动了一个终端的容器，用户通过`exit`命令或`Ctrl+d`来退出终端时，所创建的容器立刻终止。

终止状态的容器可以用`docker container ls -a`命令看到。例如

```
docker container ls -a

CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS                          PORTS               NAMES
ba267838cc1b        ubuntu:14.04             
"/bin/bash"
            30 minutes ago      Exited (0) About a minute ago                       trusting_newton
98e5efa7d997        training/webapp:latest   
"python app.py"
        About an hour ago   Exited (0) 34 minutes ago                           backstabbing_pike
```



