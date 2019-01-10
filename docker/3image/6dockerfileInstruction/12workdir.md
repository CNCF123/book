## WORKDIR {#workdir}

```
WORKDIR /path/to/workdir
```

该`WORKDIR`指令集的工作目录对任何`RUN`，`CMD`，`ENTRYPOINT`，`COPY`和`ADD`它后面的说明`Dockerfile`。如果`WORKDIR`不存在，即使它未在任何后续`Dockerfile`指令中使用，也将创建它。

该`WORKDIR`指令可以在a中多次使用`Dockerfile`。如果提供了相对路径，则它将相对于前一条`WORKDIR`指令的路径。例如：

```
WORKDIR /a

WORKDIR b

WORKDIR c

RUN pwd
```

最终`pwd`命令的输出`Dockerfile`将是`/a/b/c`。

该`WORKDIR`指令可以解析先前使用的环境变量`ENV`。您只能使用显式设置的环境变量`Dockerfile`。例如：

```
ENV DIRPATH /path

WORKDIR $DIRPATH/$DIRNAME

RUN pwd
```

最终`pwd`命令的输出`Dockerfile`将是`/path/$DIRNAME`

