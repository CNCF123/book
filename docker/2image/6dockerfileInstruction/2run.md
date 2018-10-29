### RUN

RUN有两种形式：

* 该`RUN`指令将在当前图像之上的新层中执行任何命令并提交结果。生成的提交图像将用于下一步`Dockerfile`。

分层`RUN`指令和生成提交符合Docker的核心概念，其中提交很便宜，并且可以从图像历史中的任何点创建容器，就像源代码控制一样。

在_EXEC_形式使得能够避免壳串改写（munging），并`RUN`使用不包含指定壳可执行基本图像命令。

可以使用该命令更改_shell_表单的默认shell`SHELL`。

在_shell_形式中，您可以使用`\`（反斜杠）将单个RUN指令继续到下一行。例如，考虑以下两行：

```
RUN /bin/bash -c 'source $HOME/.bashrc; \

echo $HOME'
```

它们一起相当于这一行：

```
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'
```

> **注意**：要使用除“/ bin / sh”之外的其他shell，请使用传入所需shell的_exec_表单。例如，`RUN ["/bin/bash", "-c", "echo hello"]`
>
> **注意**：_exec_表单被解析为JSON数组，这意味着您必须使用双引号（“）来围绕单词而不是单引号（'）。
>
> **注意**：与_shell_表单不同，_exec_表单不会调用命令shell。这意味着不会发生正常的shell处理。例如，`RUN [ "echo", "$HOME" ]`不会对变量进行替换`$HOME`。如果你想要shell处理，那么要么使用_shell_表单，要么直接执行shell，例如：`RUN [ "sh", "-c", "echo $HOME" ]`。当使用exec表单并直接执行shell时，就像shell表单的情况一样，它是执行环境变量扩展的shell，而不是docker。
>
> **注意**：在_JSON_表单中，必须转义反斜杠。这在反斜杠是路径分隔符的Windows上尤为重要。由于不是有效的JSON，以下行将被视为_shell_表单，并以意外方式失败：`RUN ["c:\windows\system32\tasklist.exe"]`此示例的正确语法是：`RUN ["c:\\windows\\system32\\tasklist.exe"]`

`RUN`在下一次构建期间，指令的缓存不会自动失效。类似指令的缓存`RUN apt-get dist-upgrade -y`将在下一次构建期间重用。例如，`RUN`可以通过使用`--no-cache`标志使指令的高速缓存无效`docker build --no-cache`。

有关详细信息，请参阅“[`Dockerfile`最佳实践指南](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#/build-cache)”。

`RUN`指令的高速缓存可以通过`ADD`指令无效。请参阅[下文](https://docs.docker.com/engine/reference/builder/#add)了解详情。

### 已知问题（RUN） {#known-issues-run}

* [问题783](https://github.com/docker/docker/issues/783)是关于使用AUFS文件系统时可能发生的文件权限问题。例如，您可能会在尝试`rm`文件时注意到它。

  对于具有最近aufs版本的系统（即，`dirperm1`可以设置挂载选项），docker将尝试通过使用`dirperm1`选项安装图层来自动修复问题。有关`dirperm1`选项的更多详细信息，请参见[`aufs`手册页](https://github.com/sfjro/aufs3-linux/tree/aufs3.18/Documentation/filesystems/aufs)

  如果您的系统不支持`dirperm1`，则问题描述了一种解决方法。



