## 标签选择器

pods.metadata.labels

#### 1.核心功能

基于简单且直接的标准将资源对象划分为多个较小的分组，无论是对开发人员还是对系统工程师来说，提升管理效率。

实践中，可以为资源附加多个不同纬度的标签以实现灵活的资源分组管理功能，例如，版本标签、环境标签 、 分层架构标签等，用于交叉标识同 一个资源所属的不同版本 、 环境及架构层级等 。

#### 2.标签管理

`kubectl get pods`命令中使用`--show-labels`选项，以额外显示对象的标签信息。

标签较多时，在“kubectlgetpods”命令上使用 -L Key1,key2......选项可指定显示有着特定键的标签信息。 

例如，仅显示各 pods 之上的以 env 和 release 为键名的标签:

`kubectl get pods -L env,release`

kubectl label，命令可以直接管理活动对象的标签，以按需进行添加或修改等操作。 例如，为 pod-example 添加env=prod标签:

`kubectl label pods/pod-example env=prod `

使用“ kubectl label，，为其设定新的键值时需 要为命令同时使用`--overwrite`命令以强制覆盖原有的键值。

`kubectl label pods/pod-example env=dev --overwrite`

标签选择器用于表达标签的 查 询条件或选择标准， KubernetesAPI 目前支持两个选择 器:基于等值关系\(equality-based\)以及基于集合关系\(set-based\)。

基于等值关系的标签选择器的可用操作符有“=”“==”和“!=” 三种

`kubectl get pods -l "env=prod" -L env`

基于集合关系的标签选择器支持 in、notin和exists三种操作符

`kubectl get pods -l "env in (prod , dev)"  -L env`

  
Pod对象的 spec.nodeSelector可用于定义节点标签选择器，用户 事先为特定部分的 Node资源对象设定好标签，而后配置Pod对象通过节点标签选择器进行匹配检测，从而成节点亲和性调度 。

kubectl label nodes node01.k8s.io disktype=ssd

apiVersion: v1 

kind :Pod 

metadata :

  name: pod-nodeselect

spec:

  containers:

  - name : myapp

    image: nginx 

  nodeSelector:

      disktype: ssd

