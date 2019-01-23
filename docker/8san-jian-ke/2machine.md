## docker-machine

docker技术是基于Linux内核的`cgroup`技术实现的，那么问题来了，在非Linux平台上是否就不能使用docker技术了呢？答案是可以的，不过显然需要借助虚拟机去模拟出Linux环境来。

docker-machine就是docker公司官方提出的，用于在各种平台上快速创建具有docker服务的虚拟机的技术，甚至可以通过指定driver来定制虚拟机的实现原理（一般是virtualbox）。

