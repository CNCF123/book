## CMD

3种形式：

* `CMD ["executable","param1","param2"]（执行形式，这是首选形式）`

* `CMD ["param1","param2"]（作为ENTRYPOINT的默认参数）`

* `CMD command param1 param2（贝壳形式）`

**只能有一条**`CMD`指令`Dockerfile`。如果列出多个，`CMD`则只有**最后一个**`CMD`**生效**。

**CMD是为执行容器提供默认值。**这些默认值可以包含可执行文件，也可以省略可执行文件，在这种情况下，您还必须指定一条`ENTRYPOINT`指令。

> **注意**：如果`CMD`用于为`ENTRYPOINT`指令提供默认参数，则应使用JSON数组格式指定`CMD`和`ENTRYPOINT`指令。
>
> **注意**：_exec_表单被解析为JSON数组，这意味着您必须使用双引号（“）来围绕单词而不是单引号（'）。
>
> **注意**：与_shell_表单不同，_exec_表单不会调用命令shell。这意味着不会发生正常的shell处理。例如，`CMD [ "echo", "$HOME" ]`不会对变量进行替换`$HOME`。如果你想要shell处理，那么要么使用_shell_表单，要么直接执行shell，例如：`CMD [ "sh", "-c", "echo $HOME" ]`。当使用exec表单并直接执行shell时，就像shell表单的情况一样，它是执行环境变量扩展的shell，而不是docker。

在shell或exec格式中使用时，该`CMD`指令设置在运行映像时要执行的命令。

如果你使用的是_shell的_形式`CMD`，那么`<command>`将执行`/bin/sh -c`：

```
FROM ubuntu

CMD echo "This is a test." | wc -
```

如果要**在**`<command>`**没有shell的情况下运行，**则必须将该命令表示为JSON数组，并提供可执行文件的完整路径。**此数组形式是首选格式**`CMD`**。**任何其他参数必须在数组中单独表示为字符串：

```
FROM ubuntu

CMD ["/usr/bin/wc","--help"]
```

如果您希望容器每次都运行相同的可执行文件，那么您应该考虑`ENTRYPOINT`结合使用`CMD`。请参阅[_ENTRYPOINT_](https://docs.docker.com/engine/reference/builder/#entrypoint)。

如果用户指定了参数，`docker run`那么它们将覆盖指定的默认值`CMD`。

> **注意**：不要混淆`RUN`使用`CMD`。`RUN`实际上运行一个命令并提交结果;`CMD`在构建时不执行任何操作，但指定图像的预期命令。



