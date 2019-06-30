## 容器探测

对于`Pod`的健康状态检测，`kubernetes`提供了两类探针\(Probe\)来执行对Pod的健康状态检测：

* ### LivenessProbe探针：

用于判断容器是否存活，即Pod是否为`running`状态，如果`LivenessProbe探针`探测到容器不健康，则`kubele`t将`kill`掉容器，并根据容器的重启策略是否重启，如果一个容器不包含`LivenessProbe`探针，则`Kubelet`认为容器的`LivenessProbe`探针的返回值永远成功。

* ### ReadinessProbe探针：

用于判断容器是否启动完成，即容器的`Ready`是否为`True`，可以接收请求，如果`ReadinessProbe探测`失败，则容器的`Ready`将为`False`，控制器将此`Pod`的`Endpoint`从对应的`service`的`Endpoint`列表中移除，从此不再将任何请求调度此`Pod`上，直到下次探测成功。

