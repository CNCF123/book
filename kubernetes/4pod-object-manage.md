## Pod

Pod是Kubernetes调度的最小单元。一个Pod可以包含一个或多个容器，因此它可以被看作是内部容器的逻辑宿主机。Pod的设计理念是为了支持多个容器在一个Pod中共享网络和文件系统。因此处于一个Pod中的多个容器共享以下资源：

PID命名空间：Pod中不同的应用程序可以看到其他应用程序的进程ID。

network命名空间：Pod中多个容器处于同一个网络命名空间，因此能够访问的IP和端口范围都是相同的。也可以通过localhost相互访问。

IPC命名空间：Pod中的多个容器共享Inner-process Communication命名空间，因此可以通过SystemV IPC或POSIX进行进程间通信。

UTS命名空间：Pod中的多个容器共享同一个主机名。

Volumes：Pod中各个容器可以共享在Pod中定义分存储卷（Volume）。



