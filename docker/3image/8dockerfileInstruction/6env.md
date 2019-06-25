## ENV {#env}

2种形式：

* `ENV <key> <value>`
* `ENV <key>=<value> ...`

该`ENV`指令将环境变量`<key>`设置为该值`<value>`。

例如：

```
ENV myName="John Doe" myDog="The Dog" myCat=fluffy
```

和

```
ENV myName John Doe

ENV myDog Rex The Dog

ENV myCat fluffy
```

`ENV`当从生成的图像运行容器时，使用的环境变量将保持不变。您可以使用`docker inspect`，查看值，并使用它们进行更改`docker run --env <key>=<value>`。

> **注意**：环境持久性可能会导致意外的副作用。例如，设置`ENV DEBIAN_FRONTEND noninteractive`可能会使基于Debian的图像上的apt-get用户感到困惑。要为单个命令设置值，请使用`RUN <key>=<value><command>`。



