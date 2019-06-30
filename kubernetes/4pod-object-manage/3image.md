## 镜像

#### 镜像名称

pods.spec.containers.image字段用于为其指定镜像名称

#### 获取策略

pods.spec.containers.imagePullPolicy字段用于为其指定镜像获取策略。

Always:镜像标签为“ latest”或本地镜像不存在时总是从指定的仓库中获取镜像 。

IfNotPresent: 仅当本地镜像缺失时方才从目标仓库下载镜像 。

Never:禁止从仓库下载镜像， 即仅使用本地镜像。

