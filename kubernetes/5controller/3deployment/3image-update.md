## 镜像升级

修改 Deployment控制器资源中的Pod镜像。Deployment控制器资源的修改适合使用apply和 patch命令来进行，

当然，如果仅是修改容器镜像，`set image`命令更为易用。

`kubectl set image deployments deploy-app myapp=chenkaidi/myapp:v2`

  
可以使用 `kubectl get deployments --watch `命令监控其更新过程中 Pod对象的变动过程

