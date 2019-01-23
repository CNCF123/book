## macOS 安装 Docker CE {#macos-安装-docker}

### 安装 {#系统要求}

#### 使用 Homebrew 安装 {#使用-homebrew-安装}

[Homebrew](http://brew.sh/)的[Cask](https://caskroom.github.io/)已经支持 Docker for Mac，因此可以很方便的使用 Homebrew Cask 来进行安装：

```
$ brew cask install docker
```

#### 手动下载安装 {#手动下载安装}

如果需要手动下载，请点击以下链接下载[Stable](https://download.docker.com/mac/stable/Docker.dmg)版本的 Docker for Mac。

如同 macOS 其它软件一样，安装也非常简单，双击下载的`.dmg`文件，然后将那只叫[Moby](https://blog.docker.com/2013/10/call-me-moby-dock/)的鲸鱼图标拖拽到`Application`文件夹即可（其间需要输入用户密码）。![](/assets/install-mac-dmg-1.png)

### 运行 {#运行}

从应用中找到 Docker 图标并点击运行。![](/assets/install-mac-apps-2.png)

运行之后，会在右上角菜单栏看到多了一个鲸鱼图标，这个图标表明了 Docker 的运行状态。

![](/assets/install-mac-menubar-3.png)

第一次点击图标，可能会看到这个安装成功的界面，点击 "Got it!" 可以关闭这个窗口。

![](/assets/install-mac-success-4.png)

以后每次点击鲸鱼图标会弹出操作菜单。

![](/assets/install-mac-menu-5.png)

启动终端后，通过命令可以检查安装后的 Docker 版本。

```
$ docker --version
Docker version 17.10.0-ce, build f4ffd25
$ docker-compose --version
docker-compose version 1.17.0-rc1, build a0f95af
$ docker-machine --version
docker-machine version 0.13.0, build 9ba6da9
```

如果`docker version`、`docker info`都正常的话，可以尝试运行一个[Nginx 服务器](https://store.docker.com/images/nginx/)：

```
$ docker run 
-d
 -p 80:80 --name webserver nginx
```

服务运行后，可以访问[http://localhost](http://localhost/)，如果看到了 "Welcome to nginx!"，就说明 Docker for Mac 安装成功了。![](/assets/install-mac-example-nginx-6.png)

要停止 Nginx 服务器并删除执行下面的命令：

```
$ docker stop webserver
$ docker rm webserver
```

### 镜像加速 {#镜像加速}

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，强烈建议安装 Docker 之后配置镜像加速。

本人的镜像加速 [https://5cs233bb.mirror.aliyuncs.com](https://5cs233bb.mirror.aliyuncs.com)

![](/assets/Xnip2018-11-22_13-57-16.jpg)

