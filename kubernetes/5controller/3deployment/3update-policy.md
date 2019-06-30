## 更新策略

#### 滚动更新（rolling update）

滚动更新是默认的更新策略，它在删除一部分旧版本 Pod资源的同时 ，补充创建一部分新版本的 Pod对象进行应用升级，其优势是升级期间，容器中应用提供的服务不会中断， 但要求应用程序能够应对新旧版本同时工作的情形，例如新旧版本兼容同一个数据库方案等。

滚动更新时，应用升级期间还要确保可用的Pod对象数量不低于某阔值以确保可以持续 处理客户端的服务请求，变动的方式和 Pod对象的数量范围将通过下面两个属性协同进行定义

spec.strategy.rollingUpdate.maxSurge：

指定升级期间存在的总 Pod 对象数量最多可超出期望值的个数，其值可以是 0 或正整数，也可以是一个期望值的百分比

spec.strategy.rollingUpdate.maxUnavailable：

升级期间正常可用的Pod副本数\(包括新旧版本\)最多不能低于期望数值 的个数 ，其值可以是 0 或正整数，也可以是 一个期望值的百分比，默认值为 1

还可以使用 Deplpoyment 控制器的 spec.minReadySeconds 属性来控制应用升级的速度 。

#### 重新创建（recreate）

重新创建更新类似于前文中ReplicaSet的更新方式，即首先删除现有的Pod对象，而后由控制器基于新模板重新创建出新版本。通常情况下，只应该在应 用的新旧版本不兼容\(如依赖的后端数据库的 schema不同且无法兼容\)时运行时才会使用 recreate策略 。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-myapp 
spec:
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  minReadySeconds: 5
```



