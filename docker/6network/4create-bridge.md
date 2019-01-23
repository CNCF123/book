## 自定义网桥 {#自定义网桥}

除了默认的`docker0`网桥，用户也可以指定网桥来连接各个容器。

在启动 Docker 服务的时候，使用`-b BRIDGE`或`--bridge=BRIDGE`来指定使用的网桥。

如果服务已经运行，那需要先停止服务，并删除旧的网桥。

```
systemctl stop docker
ip link set dev docker0 down
brctl del br docker0
```

然后创建一个网桥`bridge0`。

```
brctl addbr mybridge
ip addr add 192.168.1.1/24 dev mybridge
ip link set dev mybridge up
```

查看确认网桥创建并启动。

```
ip addr show mybridge
4: mybridge: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state UP group default
    link/ether 93:18:d0:0d:26:98 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.1/24 scope global mybridge
       valid_lft forever preferred_lft forever
```

在 Docker 配置文件`/etc/docker/daemon.json`中添加如下内容，即可将 Docker 默认桥接到创建的网桥上。

```
{
  "bridge": "mybridge",
}
```

启动 Docker 服务。

新建一个容器，可以看到它已经桥接到了`mybridge`上。

可以继续用`brctl show`命令查看桥接的信息。另外，在容器中可以使用`ip addr`和`ip route`命令来查看 IP 地址配置和路由信息。

