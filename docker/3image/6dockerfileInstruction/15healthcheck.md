## HEALTHCHECK

2种形式：

* `HEALTHCHECK [OPTIONS] CMD command`（通过在容器内运行命令来检查容器运行状况）
* `HEALTHCHECK NONE`（禁用从基础映像继承的任何运行状况检查）

该`HEALTHCHECK`指令告诉Docker如何测试容器以检查它是否仍在工作。即使服务器进程仍在运行，这也可以检测到陷入无限循环且无法处理新连接的Web服务器等情况。

当容器指定了运行状况检查时，除了正常状态外，它还具有运行状况。这个状态最初是`starting`。每当健康检查通过时，它就会变成`healthy`（以前的状态）。经过一定数量的连续失败后，它就变成了`unhealthy`。

之前可以出现的选项`CMD`是：

* --interval=DURATION（默认值：30s）

* --timeout=DURATION（默认值：30s）

* --start-period=DURATION（默认值：0s）

* --retries=N（默认值：3）

运行状况检查将首先在容器启动后的**间隔**秒运行，然后在每次上一次检查完成后再**间隔**秒。

如果单次运行的检查花费的时间超过**超时**秒数，那么检查将被视为失败。

它需要**重试**连续的健康检查失败才能考虑容器`unhealthy`。

**start period**为需要时间引导的容器提供初始化时间。在此期间探测失败将不计入最大重试次数。但是，如果在启动期间运行状况检查成功，则会将容器视为已启动，并且所有连续失败将计入最大重试次数。

`HEALTHCHECK`Dockerfile中只能有一条指令。如果列出多个，则只有最后一个`HEALTHCHECK`生效。

`CMD`关键字后面的命令可以是shell命令（例如`HEALTHCHECK CMD /bin/check-running`）或_exec_数组（与其他Dockerfile命令一样;`ENTRYPOINT`有关详细信息，请参阅参考资料）。

命令的退出状态指示容器的运行状况。可能的值是：

* 0：成功 - 容器健康且随时可用
* 1：不健康 - 容器无法正常工作
* 2：保留 - 不要使用此退出代码

例如，要检查每五分钟左右网络服务器能够在三秒钟内为网站的主页面提供服务：

```
HEALTHCHECK --interval=5m --timeout=3s \

  CMD curl -f http://localhost/ || exit 1
```

为了帮助调试失败的探测器，命令在stdout或stderr上写入的任何输出文本（UTF-8编码）将存储在运行状况中并可以使用查询`docker inspect`。此类输出应保持较短（目前仅存储前4096个字节）。

当容器的运行状况更改时，将`health_status`生成具有新状态的事件。

