## 后台运行 {#后台运行}

更多的时候，需要让 Docker 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。

此时，**可以通过添加**`-d`**参数来实现**。

下面举两个例子来说明一下。

如果不使用`-d`参数运行容器。

```
$ docker run ubuntu:17.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"

hello world
hello world
hello world
hello world
```

容器会把输出的结果 \(STDOUT\) 打印到宿主机上面

如果使用了`-d`参数运行容器。

```
$ docker run -d ubuntu:17.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"

77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
```

此时容器会在后台运行并不会把输出的结果 \(STDOUT\) 打印到宿主机上面\(输出结果可以用`docker logs`查看\)。







