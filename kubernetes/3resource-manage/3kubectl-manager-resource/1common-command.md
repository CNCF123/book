## 常用子命令

#### 1 kubectl的操作

kubectl作为kubernetes的命令行工具，主要的职责就是对集群中的资源的对象进行操作，这些操作包括对资源对象的创建、删除和查看等。下表中显示了kubectl支持的所有操作，以及这些操作的语法和描述信息：

| 操作 | 语法 | 描述 |
| :--- | :--- | :--- |
| annotate | kubectl annotate \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) KEY\_1=VAL\_1 … KEY\_N=VAL\_N \[–overwrite\] \[–all\] \[–resource-version=version\] \[flags\] | 添加或更新一个或多个资源的注释 |
| api-versions | kubectl api-versions \[flags\] | 列出可用的API版本 |
| apply | kubectl apply -f FILENAME \[flags\] | 将来自于文件或stdin的配置变更应用到主要对象中。 |
| attach | kubectl attach POD -c CONTAINER \[-i\] \[-t\] \[flags\] | 连接到正在运行的容器上，以查看输出流或与容器交互（stdin）。 |
| autoscale | kubectl autoscale \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) \[–min=MINPODS\] –max=MAXPODS \[–cpu-percent=CPU\] \[flags\] | 自动扩宿容由副本控制器管理的Pod。 |
| cluster-info | kubectl cluster-info \[flags\] | 显示群集中的主节点和服务的的端点信息。 |
| config | kubectl config SUBCOMMAND \[flags\] | 修改kubeconfig文件。 |
| create | kubectl create -f FILENAME \[flags\] | 从文件或stdin中创建一个或多个资源对象。 |
| delete | kubectl delete \(-f FILENAME \\| TYPE \[NAME \\| /NAME \\| -l label \\| –all\]\) \[flags\] | 删除资源对象。 |
| describe | kubectl describe \(-f FILENAME \\| TYPE \[NAME\_PREFIX \\| /NAME \\| -l label\]\) \[flags\] | 显示一个或者多个资源对象的详细信息。 |
| edit | kubectl edit \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) \[flags\] | 通过默认编辑器编辑和更新服务器上的一个或多个资源对象。 |
| exec | kubectl exec POD \[-c CONTAINER\] \[-i\] \[-t\] \[flags\] \[– COMMAND \[args…\]\] | 在Pod的容器中执行一个命令。 |
| explain | kubectl explain \[–include-extended-apis=true\] \[–recursive=false\] \[flags\] | 获取Pod、Node和服务等资源对象的文档。 |
| expose | kubectl expose \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) \[–port=port\] \[–protocol=TCP\\|UDP\] \[–target-port=number-or-name\] \[–name=name\] \[—-external-ip=external-ip-of-service\] \[–type=type\] \[flags\] | 为副本控制器、服务或Pod等暴露一个新的服务。 |
| get | kubectl get \(-f FILENAME \\| TYPE \[NAME \\| /NAME \\| -l label\]\) \[–watch\] \[–sort-by=FIELD\] \[\[-o \\| –output\]=OUTPUT\_FORMAT\] \[flags\] | 列出一个或多个资源。 |
| label | kubectl label \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) KEY\_1=VAL\_1 … KEY\_N=VAL\_N \[–overwrite\] \[–all\] \[–resource-version=version\] \[flags\] | 添加或更新一个或者多个资源对象的标签。 |
| logs | kubectl logs POD \[-c CONTAINER\] \[–follow\] \[flags\] | 显示Pod中一个容器的日志。 |
| patch | kubectl patch \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) –patch PATCH \[flags\] | 使用策略合并补丁过程更新资源对象中的一个或多个字段。 |
| port-forward | kubectl port-forward POD \[LOCAL\_PORT:\]REMOTE\_PORT \[…\[LOCAL\_PORT\_N:\]REMOTE\_PORT\_N\] \[flags\] | 将一个或多个本地端口转发到Pod。 |
| proxy | kubectl proxy \[–port=PORT\] \[–www=static-dir\] \[–www-prefix=prefix\] \[–api-prefix=prefix\] \[flags\] | 为kubernetes API服务器运行一个代理。 |
| replace | kubectl replace -f FILENAME | 从文件或stdin中替换资源对象。 |
| rolling-update | kubectl rolling-update OLD\_CONTROLLER\_NAME \(\[NEW\_CONTROLLER\_NAME\] –image=NEW\_CONTAINER\_IMAGE \\| -f NEW\_CONTROLLER\_SPEC\) \[flags\] | 通过逐步替换指定的副本控制器和Pod来执行滚动更新。 |
| run | kubectl run NAME –image=image \[–env=”key=value”\] \[–port=port\] \[–replicas=replicas\] \[–dry-run=bool\] \[–overrides=inline-json\] \[flags\] | 在集群上运行一个指定的镜像。 |
| scale | kubectl scale \(-f FILENAME \\| TYPE NAME \\| TYPE/NAME\) –replicas=COUNT \[–resource-version=version\] \[–current-replicas=count\] \[flags\] | 扩宿容副本集的数量。 |
| version | kubectl version \[–client\] \[flags\] | 显示运行在客户端和服务器端的Kubernetes版本。 |

#### 2 资源对象类型

在kubernetes中，提供了很多的资源对象，开发和运维人员可以通过这些对象对容器进行编排。在下表中，是kubectl所支持的资源对象类型，以及它们的缩略别名：

| 资源对象类型 | 缩略别名 |
| :--- | :--- |
| apiservices |  |
| certificatesigningrequests | csr |
| clusters |  |
| clusterrolebindings |  |
| clusterroles |  |
| componentstatuses | cs |
| configmaps | cm |
| controllerrevisions |  |
| cronjobs |  |
| customresourcedefinition | crd |
| daemonsets | ds |
| deployments | deploy |
| endpoints | ep |
| events | ev |
| horizontalpodautoscalers | hpa |
| ingresses | ing |
| jobs |  |
| limitranges | limits |
| namespaces | ns |
| networkpolicies | netpol |
| nodes | no |
| persistentvolumeclaims | pvc |
| persistentvolumes | pv |
| poddisruptionbudget | pdb |
| podpreset |  |
| pods | po |
| podsecuritypolicies | psp |
| podtemplates |  |
| replicasets | rs |
| replicationcontrollers | rc |
| resourcequotas | quota |
| rolebindings |  |
| roles |  |
| secrets |  |
| serviceaccounts | sa |
| services | svc |
| statefulsets |  |
| storageclasses |  |

#### 3 输出选项

kubectl默认的输出格式为纯文本格式，可以通过-o或者–output字段指定命令的输出格式。

```
$ kubectl [command][TYPE][NAME]-o=<output_format>
```

| 输出格式 | 描述 |
| :--- | :--- |
| -o=custom-columns=&lt;spec&gt; | 使用以逗号分隔的自定义列打印表格。 |
| -o=custom-columns-file=&lt;filename&gt; | 使用文件中自定义列打印表格。 |
| -o=json | 输出JSON格式的API对象 |
| -o=jsonpath=&lt;template&gt; | 打印在jsonpath表达式中定义的字段 |
| -o=jsonpath-file=&lt;filename&gt; | 打印文件中以jsonpath表达式定义的字段 |
| -o=name | 仅仅输出资源对象的名称。 |
| -o=wide | 输出带有附加信息的纯文本格式。对于Pod对象，将会包含Node名称。 |
| -o=yaml | 输出YAML格式的API对象 |





