## 进入容器 {#进入容器}

在使用`-d`参数时，容器启动后会进入后台。

某些时候需要进入容器进行操作，包括使用`docker attach`命令或`docker exec`命令，推荐大家使用`docker exec`命令。

### `exec`命令 {#exec-命令}

#### -i -t 参数，结合使用 -it {#i--t-参数}

`docker exec`后边可以跟多个参数，这里主要说明`-i-t`参数。

只用`-i`参数时，由于没有分配伪终端，界面没有我们熟悉的 Linux 命令提示符，但命令执行结果仍然可以返回。

当`-it`参数一起使用时，则可以看到我们熟悉的 Linux 命令提示符。

```
$ docker run -dit ubuntu
bb7d14c27fe9c094717715f4f8a69d554b6ffa0127ada690f4523fe319cd7760

$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
bb7d14c27fe9        ubuntu              "/bin/bash"         28 seconds ago      Up 26 seconds                           inspiring_cocks
bin boot dev ...

$ docker exec -it bb7d14c27fe9 /bin/sh
# ls
bin   dev  home  lib64    mnt  proc  run     srv  tmp  var
boot  etc  lib     media    opt  root  sbin  sys  usr
```

从上面的ls命令可以看到，容器内部的文件系统与host主机的文件类似。

如果从这个 stdin 中 exit，不会导致容器的停止。这就是为什么推荐大家使用`docker exec`的原因。

更多参数说明请使用`docker exec --help`查看。

