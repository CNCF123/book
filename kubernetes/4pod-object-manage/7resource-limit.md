## 资源限制

Pod请求或消费的“计算资源”是指CPU和内存， 这也是目前仅有的受支持的两种类型 。 相比较来说， CPU 属于可压缩资源，而内存是不可压缩型资源，对其执行收缩操作可能会导致某种程度的问题。

目前来说，资源隔离尚且属于容器级别， CPU 和内存资源的配置需要在 Pod 中的容器 上进行，每种资源均可由“ requests”属性定义其请求的确保可用值，即容器运行可能用不 到这些额度的资源，但用到时必须要确保有如此多的资源可用，而“ limits”属性则用于限 制资源可用的最大值，即硬限制 。

在Kubernetes系统上，1个单位的CPU相当于虚拟机上的1颗虚拟CPU\(vCPU\)或物 理机上的一个超线程\( Hyperthread，或称为一个逻辑 CPU\)，它支持分数计量方式， 一个核 心\(1core\)相当于1000个微核心\(millicores\)，因此500m相当于是0.5个核心。 内存的计量方式与日常使用方式相同，默认单位是字节，也可以使用 T、G、 M作为单位后缀，或Ti、Gi、Mi形式的单位后缀。



```
apiVersion: v1
kind: Pod 
metadata:
  name: Name
spec:
  containers:
  - name: nginx
    image: nginx    
    resources:
      limits:
        cpu:  100m
        memory: 1000Mi
      requests:
        cpu: 50m
        memory: 500Mi
```



