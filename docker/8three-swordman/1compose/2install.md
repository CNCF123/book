## compose安装

在 Linux 上的也安装十分简单，从官方 GitHub 处直接下载编译好的二进制文件即可。

例如，在 Linux 64 位系统上直接下载对应的二进制包。

1.运行此命令以下载最新版本的Docker Compose：

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2.对二进制文件应用可执行权限：

```
sudo chmod +x /usr/local/bin/docker-compose
```


