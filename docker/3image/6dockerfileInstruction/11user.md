## USER

2种形式：

* `USER <user>[:<group>]`
* `USER <UID>[:<GID>]`

的`USER`运行的图像和用于当任何指令设置的用户名（或UID）和任选的所述用户组（或GID）使用`RUN`，`CMD`和`ENTRYPOINT`它后面的指令`Dockerfile`。

> **警告**：当用户没有主要组时，图像（或下一个说明）将与该`root`组一起运行。
>
> 在Windows上，如果用户不是内置帐户，则必须先创建用户。这可以通过`net user`作为Dockerfile的一部分调用的命令来完成。

```
    FROM microsoft/windowsservercore

    # Create Windows user in the container

    RUN net user /add patrick

    # Set it for subsequent commands

    USER patrick
```



