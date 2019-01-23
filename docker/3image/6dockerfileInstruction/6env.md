## ENV {#env}

2种形式：

* `ENV <key> <value>`
* `ENV <key>=<value> ...`

该`ENV`指令将环境变量`<key>`设置为该值`<value>`。此值将在构建阶段中所有后续指令的环境中，并且也可以在许多[内联替换](https://docs.docker.com/engine/reference/builder/#environment-replacement)。

该`ENV`指令有两种形式。第一种形式，`ENV <key><value>`将一个变量设置为一个值。第一个空格后的整个字符串将被视为`<value>`- 包括空格字符。该值将针对其他环境变量进行解释，因此如果未对其进行转义，则将删除引号字符。

第二种形式`ENV <key>=<value> ...`允许一次设置多个变量。请注意，第二种形式在语法中使用等号（=），而第一种形式则不然。与命令行解析一样，引号和反斜杠可用于在值内包含空格。

例如：

```
ENV myName="John Doe" myDog=Rex\ The\ Dog \myCat=fluffy
```

和

```
ENV myName John Doe

ENV myDog Rex The Dog

ENV myCat fluffy
```

将在最终图像中产生相同的净结果。

`ENV`当从生成的图像运行容器时，使用的环境变量将保持不变。您可以使用`docker inspect`，查看值，并使用它们进行更改`docker run --env <key>=<value>`。

> **注意**：环境持久性可能会导致意外的副作用。例如，设置`ENV DEBIAN_FRONTEND noninteractive`可能会使基于Debian的图像上的apt-get用户感到困惑。要为单个命令设置值，请使用`RUN <key>=<value><command>`。



