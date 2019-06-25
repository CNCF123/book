## 导入导出镜像

### 导出镜像

例如：导出本地的 ubuntu:16.04 镜像为文件 ubuntu-16.04.tar

\# docker save -o ubuntu-16.04.tar ubuntu:16.04

用户就可以通过复制 ubuntu-16.04.tar 文件将该镜像分享给他人 。

### 导入镜像

可以使用 docker load将导出的 tar 文件再导人到本地镜像库。

例如，从文件 ubuntu-16.04.tar 导人镜像到本地镜像列表，如下所示:

\#  docker load -i ubuntu-16.04.tar

或者 :

\# docker load &lt; ubuntu-16.04.tar

### 

### 



