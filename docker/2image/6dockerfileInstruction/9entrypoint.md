## ENTRYPOINT

ENTRYPOINT有两种形式：

* `ENTRYPOINT ["executable", "param1", "param2"] （执行形式，首选）`

* `ENTRYPOINT command param1 param2 （贝壳形式）`

An`ENTRYPOINT`允许您配置将作为可执行文件运行的容器。

例如，以下将使用其默认内容启动nginx，侦听端口80：

```
docker run -i -t --rm -p 80:80 nginx
```

命令行参数`docker run <image>`将附加在_exec_表单中的所有元素之后`ENTRYPOINT`，并将覆盖使用的所有指定元素`CMD`。这允许将参数传递给入口点，`docker run <image> -d`即将`-d`参数传递给入口点。您可以`ENTRYPOINT`使用`docker run --entrypoint`标志覆盖指令。

所述_壳_形式防止任何`CMD`或`run`被使用命令行参数，但是具有你的缺点`ENTRYPOINT`将被开始作为一个子命令`/bin/sh -c`，其不通过信号。这意味着可执行文件将不是容器`PID 1`- 并且_不会_收到Unix信号 - 因此您的可执行文件将不会收到`SIGTERM`来自`docker stop <container>`。

只有意志中的最后一条`ENTRYPOINT`指令`Dockerfile`才有效。

### Exec形成ENTRYPOINT示例 {#exec-form-entrypoint-example}

您可以使用_exec_形式`ENTRYPOINT`设置相当稳定的默认命令和参数，然后使用任一形式`CMD`设置更可能更改的其他默认值。

```
FROM ubuntu

ENTRYPOINT ["top", "-b"]

CMD ["-c"]
```

运行容器时，您可以看到这`top`是唯一的进程：

```
$ docker run -it --rm --name test  top -H

top - 08:25:00 up  7:27,  0 users,  load average: 0.00, 0.01, 0.05

Threads:   1 total,   1 running,   0 sleeping,   0 stopped,   0 zombie

%Cpu(s):  0.1 us,  0.1 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st

KiB Mem:   2056668 total,  1616832 used,   439836 free,    99352 buffers

KiB Swap:  1441840 total,        0 used,  1441840 free.  1324440 cached Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND

    1 root      20   0   19744   2336   2080 R  0.0  0.1   0:00.04 top
```

要进一步检查结果，您可以使用`docker exec`：

```
$ docker exec -it test ps aux

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND

root         1  2.6  0.1  19752  2352 ?        Ss+  08:24   0:00 top -b -H

root         7  0.0  0.1  15572  2164 ?        R+   08:25   0:00 ps aux
```

并且您可以优雅地请求`top`关闭使用`docker stop test`。

以下`Dockerfile`显示使用`ENTRYPOINT`在前台运行Apache（即as`PID 1`）：

```
FROM debian:stable

RUN apt-get update 
&
&
 apt-get install -y --force-yes apache2

EXPOSE 80 443

VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]

ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

如果需要为单个可执行文件编写启动脚本，可以使用`exec`和`gosu`命令确保最终的可执行文件接收Unix信号：

```
#!/usr/bin/env bash
set
-e
if
[
"
$1
"
=
'postgres'
]
;
then

chown 
-R
 postgres 
"
$PGDATA
"
if
[
-z
"
$(
ls
-A
"
$PGDATA
"
)
"
]
;
then

gosu postgres initdb

fi


exec 
gosu postgres 
"
$@
"
fi


exec
"
$@
"
```

最后，如果您需要在关机时进行一些额外的清理（或与其他容器通信），或者协调多个可执行文件，您可能需要确保`ENTRYPOINT`脚本接收Unix信号，传递它们，然后执行一些更多的工作：

```
#!/bin/sh
# Note: I've written this using sh so it works in the busybox container too
# USE the trap if you need to also do manual cleanup after the service is stopped,
#     or need to start multiple services in the one container
trap
"echo TRAPed signal"
 HUP INT QUIT TERM
# start service in background here

/usr/sbin/apachectl start
echo
"[hit enter key to exit] or run 'docker stop 
<
container
>
'"
read
# stop service and clean up here
echo
"stopping apache"

/usr/sbin/apachectl stop
echo
"exited 
$0
"
```

如果运行此映像`docker run -it --rm -p 80:80 --name test apache`，则可以使用`docker exec`，或检查容器的进程`docker top`，然后请求脚本停止Apache：

```
$ 
docker 
exec
-it
test 
ps aux

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND

root         1  0.1  0.0   4448   692 ?        Ss+  00:42   0:00 /bin/sh /run.sh 123 cmd cmd2

root        19  0.0  0.2  71304  4440 ?        Ss   00:42   0:00 /usr/sbin/apache2 
-k
 start

www-data    20  0.2  0.2 360468  6004 ?        Sl   00:42   0:00 /usr/sbin/apache2 
-k
 start

www-data    21  0.2  0.2 360468  6000 ?        Sl   00:42   0:00 /usr/sbin/apache2 
-k
 start

root        81  0.0  0.1  15572  2140 ?        R+   00:44   0:00 ps aux
$ 
docker top 
test

PID                 USER                COMMAND

10035               root                
{
run.sh
}
 /bin/sh /run.sh 123 cmd cmd2

10054               root                /usr/sbin/apache2 
-k
 start

10055               33                  /usr/sbin/apache2 
-k
 start

10056               33                  /usr/sbin/apache2 
-k
 start

$ 
/usr/bin/time docker stop 
test
test

real    0m 0.27s

user    0m 0.03s

sys    0m 0.03s
```

> **注意：**您可以使用覆盖`ENTRYPOINT`设置`--entrypoint`，但这只能将二进制设置为_exec_（不会`sh -c`使用）。
>
> **注意**：_exec_表单被解析为JSON数组，这意味着您必须使用双引号（“）来围绕单词而不是单引号（'）。
>
> **注意**：与_shell_表单不同，_exec_表单不会调用命令shell。这意味着不会发生正常的shell处理。例如，`ENTRYPOINT [ "echo", "$HOME" ]`不会对变量进行替换`$HOME`。如果你想要shell处理，那么要么使用_shell_表单，要么直接执行shell，例如：`ENTRYPOINT [ "sh", "-c", "echo $HOME" ]`。当使用exec表单并直接执行shell时，就像shell表单的情况一样，它是执行环境变量扩展的shell，而不是docker。

### Shell表格ENTRYPOINT示例 {#shell-form-entrypoint-example}

您可以为`ENTRYPOINT`它指定一个纯字符串，它将在其中执行`/bin/sh -c`。此表单将使用shell处理来替换shell环境变量，并将忽略任何`CMD`或`docker run`命令行参数。为了确保能够正确地`docker stop`发出任何长时间运行的`ENTRYPOINT`可执行文件，您需要记住启动它`exec`：

```
FROM ubuntu

ENTRYPOINT exec top -b
```

运行此图像时，您将看到单个`PID 1`进程：

```
$ docker run -it --rm --name test top

Mem: 1704520K used, 352148K free, 0K shrd, 0K buff, 140368121167873K cached

CPU:   5% usr   0% sys   0% nic  94% idle   0% io   0% irq   0% sirq

Load average: 0.08 0.03 0.05 2/98 6

  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND

    1     0 root     R     3164   0%   0% top -b
```

哪个将彻底退出`docker stop`：

```
$ /usr/bin/time docker stop test

test

real    0m 0.20s

user    0m 0.02s

sys    0m 0.04s
```

如果您忘记添加`exec`到您的开头`ENTRYPOINT`：

```
FROM ubuntu

ENTRYPOINT top -b

CMD --ignored-param1
```

然后，您可以运行它（为下一步命名）：

```
$ docker run -it --name test top --ignored-param2

Mem: 1704184K used, 352484K free, 0K shrd, 0K buff, 140621524238337K cached

CPU:   9% usr   2% sys   0% nic  88% idle   0% io   0% irq   0% sirq

Load average: 0.01 0.02 0.05 2/101 7

  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND

    1     0 root     S     3168   0%   0% /bin/sh -c top -b cmd cmd2

    7     1 root     R     3164   0%   0% top -b
```

您可以从输出中`top`看到指定`ENTRYPOINT`的不是`PID 1`。

如果然后运行`docker stop test`，容器将不会干净地退出 -`stop`命令将被强制`SIGKILL`在超时后发送：

```
$ docker exec -it test ps aux

PID   USER     COMMAND

    1 root     /bin/sh -c top -b cmd cmd2

    7 root     top -b

    8 root     ps aux

$ /usr/bin/time docker stop test

test

real    0m 10.19s

user    0m 0.04s

sys    0m 0.03s
```

### 了解CMD和ENTRYPOINT如何相互作用 {#understand-how-cmd-and-entrypoint-interact}

Both`CMD`和`ENTRYPOINT`instructions指定运行容器时执行的命令。很少有规则描述他们的合作。

1. Dockerfile应至少指定一个`CMD`或`ENTRYPOINT`命令。

2. `ENTRYPOINT`应该在将容器用作可执行文件时定义。

3. `CMD`应该用作定义`ENTRYPOINT`命令的默认参数或在容器中执行ad-hoc命令的方法。

4. `CMD`在使用替代参数运行容器时将被覆盖。

下表显示了针对不同`ENTRYPOINT`/`CMD`组合执行的命令：

|  | 没有ENTRYPOINT | ENTRYPOINT exec\_entry p1\_entry | ENTRYPOINT \[“exec\_entry”，“p1\_entry”\] |
| :--- | :--- | :--- | :--- |
| **没有CMD** | _错误，不允许_ | / bin / sh -c exec\_entry p1\_entry | exec\_entry p1\_entry |
| **CMD \[“exec\_cmd”，“p1\_cmd”\]** | exec\_cmd p1\_cmd | / bin / sh -c exec\_entry p1\_entry | exec\_entry p1\_entry exec\_cmd p1\_cmd |
| **CMD \[“p1\_cmd”，“p2\_cmd”\]** | p1\_cmd p2\_cmd | / bin / sh -c exec\_entry p1\_entry | exec\_entry p1\_entry p1\_cmd p2\_cmd |
| **CMD exec\_cmd p1\_cmd** | / bin / sh -c exec\_cmd p1\_cmd | / bin / sh -c exec\_entry p1\_entry | exec\_entry p1\_entry / bin / sh -c exec\_cmd p1\_cmd |



