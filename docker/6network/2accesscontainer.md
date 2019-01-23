## 外部访问容器 {#外部访问容器}

容器中运行一些网络应用，让外部也可以访问这些应用，可以通过`-P`或`-p`参数来指定端口映射。

当使用`-P(大)`标记时，Docker 会随机映射一个 **32768-61000** 的端口到内部容器开放的网络端口。

使用`docker container ls`可以看到，本地主机的 49155 被映射到了容器的 5000 端口。此时访问本机的 49155 端口即可访问容器内 web 应用提供的界面。

```
docker run -d -P training/webapp python app.py

docker container ls -l

CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse
```

### 映射所有接口地址 {#映射所有接口地址}

使用**`hostPort:containerPort`**格式本地的 5000 端口映射到容器的 5000 端口，可以执行

```
docker run -d -p 5000:5000 training/webapp python app.py
```

此时默认会绑定本地所有接口上的所有地址。

### 映射到指定地址的指定端口 {#映射到指定地址的指定端口}

可以使用**`ip:hostPort:containerPort`**格式指定映射使用一个特定地址，比如 localhost 地址 127.0.0.1

```
docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
```

### 映射到指定地址的任意端口 {#映射到指定地址的任意端口}

使用**`ip::containerPort`**绑定 localhost 的任意端口到容器的 5000 端口，本地主机会自动分配一个端口。

```
docker run -d -p 127.0.0.1::5000 training/webapp python app.py
```

还可以使用**`udp`**标记来指定`udp`端口

```
docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
```

### 查看映射端口配置 {#查看映射端口配置}

使用`docker port`来查看当前映射的端口配置，也可以查看到绑定的地址

```
docker port nostalgic_morse 5000
127.0.0.1:49155.
```

注意：

* 容器有自己的内部网络和 ip 地址（使用`docker inspect`可以获取所有的变量，Docker 还可以有一个可变的网络配置。）

* `-p`标记可以多次使用来绑定多个端口

例如

```
docker run -d \
    -p 5000:5000 \
    -p 3000:80 \
    training/webapp \
    python app.py
```



