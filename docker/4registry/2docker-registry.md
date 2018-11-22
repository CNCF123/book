## 私有仓库 {#私有仓库}

有时候使用 Docker Hub 这样的公共仓库可能不方便，用户可以创建一个本地仓库供私人使用。

本节介绍如何使用本地仓库。

[`docker-registry`](https://docs.docker.com/registry/)是官方提供的工具，可以用于构建私有的镜像仓库。本文内容基于[`docker-registry`](https://github.com/docker/distribution)v2.x 版本。

### 安装运行 docker-registry {#安装运行-docker-registry}

#### 容器运行 {#容器运行}

你可以通过获取官方`registry`镜像来运行。

```
$ docker run -d -p 5000:5000 --restart=always --name registry registry
```

这将使用官方的`registry`镜像来启动私有仓库。默认情况下，仓库会被创建在容器的`/var/lib/registry`目录下。你可以通过`-v`参数来将镜像文件存放在本地的指定路径。例如下面的例子将上传的镜像放到本地的`/opt/data/registry`目录。

```
$ docker run -d \
    -p 5000:5000 \
    -v /opt/data/registry:/var/lib/registry \
    registry
```

### 在私有仓库上传、搜索、下载镜像 {#在私有仓库上传、搜索、下载镜像}

创建好私有仓库之后，就可以使用`docker tag`来标记一个镜像，然后推送它到仓库。例如私有仓库地址为`127.0.0.1:5000`。

先在本机查看已有的镜像。

```
$ docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu                            latest              ba5877dc9bec        6 weeks ago         192.7 MB
```

##### 使用**`docker tag`**将`ubuntu:latest`这个镜像标记为`127.0.0.1:5000/ubuntu:latest`。

格式为`docker tag IMAGE[:TAG] [REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY[:TAG]`。

```
$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
$ docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu                            latest              ba5877dc9bec        6 weeks ago         192.7 MB
127.0.0.1:5000/ubuntu:latest      latest              ba5877dc9bec        6 weeks ago         192.7 MB
```

使用**`docker push`**上传标记的镜像。

```
$ docker push 127.0.0.1:5000/ubuntu:latest
The push refers to repository [127.0.0.1:5000/ubuntu]
373a30c24545: Pushed
a9148f5200b0: Pushed
cdd3de0940ab: Pushed

fc
56279bbb33: Pushed
b38367233d37: Pushed
2aebd096e0e2: Pushed
latest: digest: sha256:fe4277621f10b5026266932ddf760f5a756d2facd505a94d2da12f4f52f71f5a size: 1568
```

用`curl`查看仓库中的镜像。

```
$ curl 127.0.0.1:5000/v2/_catalog
{"repositories":["ubuntu"]}
```

这里可以看到`{"repositories":["ubuntu"]}`，表明镜像已经被成功上传了。

先删除已有镜像，再尝试从私有仓库中下载这个镜像。

```
$ docker image rm 127.0.0.1:5000/ubuntu:latest

$ docker pull 127.0.0.1:5000/ubuntu:latest
Pulling repository 127.0.0.1:5000/ubuntu:latest
ba5877dc9bec: Download complete
511136ea3c5a: Download complete
9bad880da3d2: Download complete
25f11f5fb0cb: Download complete
ebc34468f71d: Download complete
2318d26665ef: Download complete

$ docker image ls
REPOSITORY                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
127.0.0.1:5000/ubuntu:latest       latest              ba5877dc9bec        6 weeks ago         192.7 MB
```

### 注意事项 {#注意事项}

如果你不想使用`127.0.0.1:5000`作为仓库地址，比如想让本网段的其他主机也能把镜像推送到私有仓库。你就得把例如`192.168.199.100:5000`这样的内网地址作为私有仓库地址，这时你会发现无法成功推送镜像。

这是因为 Docker 默认不允许非`HTTPS`方式推送镜像。我们可以通过 Docker 的配置选项来取消这个限制，或者查看下一节配置能够通过`HTTPS`访问的私有仓库。

#### Ubuntu 14.04, Debian 7 Wheezy {#ubuntu-1404-debian-7-wheezy}

对于使用`upstart`的系统而言，编辑`/etc/default/docker`文件，在其中的`DOCKER_OPTS`中增加如下内容：

```
DOCKER_OPTS=
"--registry-mirror=https://registry.docker-cn.com --insecure-registries=192.168.199.100:5000"
```

重新启动服务。

```
$ sudo service docker restart
```

#### Ubuntu 16.04+, Debian 8+, centos 7 {#ubuntu-1604-debian-8-centos-7}

对于使用`systemd`的系统，请在`/etc/docker/daemon.json`中写入如下内容（如果文件不存在请新建该文件）

```
{

  "registry-mirror": [

    "https://registry.docker-cn.com"
  ],

  "insecure-registries": [

    "192.168.1.100:5000"
  ]
}
```

> 注意：该文件必须符合`json`规范，否则 Docker 将不能启动。

对于 Docker for Windows 、 Docker for Mac 在设置中编辑`daemon.json`增加和上边一样的字符串即可。

## 高级配置 {#私有仓库高级配置}

上一节我们搭建了一个具有基础功能的私有仓库，本小节我们来使用`Docker Compose`搭建一个拥有权限认证、TLS 的私有仓库。

新建一个文件夹，以下步骤均在该文件夹中进行。

### 准备站点证书 {#准备站点证书}

如果你拥有一个域名，国内各大云服务商均提供免费的站点证书。你也可以使用`openssl`自行签发证书。

这里假设我们将要搭建的私有仓库地址为`docker.domain.com`，下面我们介绍使用`openssl`自行签发`docker.domain.com`的站点 SSL 证书。

第一步创建`CA`私钥。

```
$ openssl genrsa -out "root-ca.key" 4096
```

第二步利用私钥创建`CA`根证书请求文件。

```
$ openssl req \
          -new -key "root-ca.key" \
          -out "root-ca.csr" -sha256 \
          -subj '/C=CN/ST=Shanxi/L=Datong/O=Your Company Name/CN=Your Company Name Docker Registry CA'
```

> 以上命令中`-subj`参数里的`/C`表示国家，如`CN`；`/ST`表示省；`/L`表示城市或者地区；`/O`表示组织名；`/CN`通用名称。

第三步配置`CA`根证书，新建`root-ca.cnf`。

```
[root_ca]
basicConstraints = critical,CA:TRUE,pathlen:1
keyUsage = critical, nonRepudiation, cRLSign, keyCertSign
subjectKeyIdentifier=
hash
```

第四步签发根证书。

```
$ openssl x509 -req  -days 3650  -in "root-ca.csr" \
         -signkey "root-ca.key" -sha256 -out "root-ca.crt" \
         -extfile "root-ca.cnf"
         -extensions \
         root_ca
```

第五步生成站点`SSL`私钥。

```
$ openssl genrsa -out 
"docker.domain.com.key"
 4096
```

第六步使用私钥生成证书请求文件。

```
$ openssl req -new -key 
"docker.domain.com.key"
 -out 
"site.csr"
 -sha256 \
          -subj 
'/C=CN/ST=Shanxi/L=Datong/O=Your Company Name/CN=docker.domain.com'
```

第七步配置证书，新建`site.cnf`文件。

```
[server]
authorityKeyIdentifier=keyid,issuer
basicConstraints = critical,CA:FALSE
extendedKeyUsage=serverAuth
keyUsage = critical, digitalSignature, keyEncipherment
subjectAltName = DNS:docker.domain.com, IP:127.0.0.1
subjectKeyIdentifier=
hash
```

第八步签署站点`SSL`证书。

```
$ openssl x509 -req -days 750 -in 
"site.csr"
 -sha256 \
    -CA 
"root-ca.crt"
 -CAkey 
"root-ca.key"
  -CAcreateserial \
    -out 
"docker.domain.com.crt"
 -extfile 
"site.cnf"
 -extensions server
```

这样已经拥有了`docker.domain.com`的网站 SSL 私钥`docker.domain.com.key`和 SSL 证书`docker.domain.com.crt`及 CA 根证书`root-ca.crt`。

新建`ssl`文件夹并将`docker.domain.com.keydocker.domain.com.crtroot-ca.crt`这三个文件移入，删除其他文件。

### 配置私有仓库 {#配置私有仓库}

私有仓库默认的配置文件位于`/etc/docker/registry/config.yml`，我们先在本地编辑`config.yml`，之后挂载到容器中。

```
version:
0.1
log:
  accesslog:
    disabled:
true
  level:
 debug

  formatter:
 text

  fields:
    service:
 registry

    environment:
 staging

storage:
  delete:
    enabled:
true
  cache:
    blobdescriptor:
 inmemory

  filesystem:
    rootdirectory:
 /var/lib/registry

auth:
  htpasswd:
    realm:
 basic-realm

    path:
 /etc/docker/registry/auth/nginx.htpasswd

http:
  addr:
 :
443
  host:
 https://docker.domain.com

  headers:
    X-Content-Type-Options:
 [nosniff]

  http2:
    disabled:
false
  tls:
    certificate:
 /etc/docker/registry/ssl/docker.domain.com.crt

    key:
 /etc/docker/registry/ssl/docker.domain.com.key

health:
  storagedriver:
    enabled:
true
    interval:
10
s

threshold:
3
```

### 生成 http 认证文件 {#生成-http-认证文件}

```
$ mkdir auth

$ docker run --rm \
    --entrypoint htpasswd \
    registry \
    -Bbn username password 
>
 auth/nginx.htpasswd
```

> 将上面的`usernamepassword`替换为你自己的用户名和密码。

### 编辑`docker-compose.yml` {#编辑-docker-composeyml}

```
version:
'3'
services:
  registry:
    image:
 registry

    ports:
      -
"443:443"
    volumes:
      -
 ./:/etc/docker/registry

      - registry-data:
/var/lib/registry


volumes:
  registry-data:
```

### 修改 hosts {#修改-hosts}

编辑`/etc/hosts`

```
127.0.0.1 docker.domain.com
```

### 启动 {#启动}

```
$ docker-compose up 
-d
```

这样我们就搭建好了一个具有权限认证、TLS 的私有仓库，接下来我们测试其功能是否正常。

### 测试私有仓库功能 {#测试私有仓库功能}

由于自行签发的 CA 根证书不被系统信任，所以我们需要将 CA 根证书`ssl/root-ca.crt`移入`/etc/docker/certs.d/docker.domain.com`文件夹中。

```
$ sudo mkdir -p /etc/docker/certs.d/docker.domain.com

$ sudo cp ssl/root-ca.crt /etc/docker/certs.d/docker.domain.com/ca.crt
```

登录到私有仓库。

```
$ docker login docker.domain.com
```

尝试推送、拉取镜像。

```
$ docker pull ubuntu:18.04

$ docker tag ubuntu:18.04 docker.domain.com/username/ubuntu:18.04

$ docker push docker.domain.com/username/ubuntu:18.04

$ docker image rm docker.domain.com/username/ubuntu:18.04

$ docker pull docker.domain.com/username/ubuntu:18.04
```

如果我们退出登录，尝试推送镜像。

```
$ docker 
logout
 docker.domain.com

$ docker push docker.domain.com/username/ubuntu:18.04

no basic auth credentials
```

发现会提示没有登录，不能将镜像推送到私有仓库中。

### 注意事项 {#注意事项}

如果你本机占用了`443`端口，你可以配置[Nginx 代理](https://docs.docker.com/registry/recipes/nginx/)，这里不再赘述。

