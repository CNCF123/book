## readinessProbe

与存活性探测机制相同，就绪性探测也支持 Exec、 HTTPGET和TCPSocket三种探测方式，且各自的定义机制也都相同 。

但与存活性探测触发的操作不同的是，探测失败时，就绪性探测不会杀死或重启容器以保证其健康性，而是通知其尚未就绪，并触发依赖于其就 绪状态的操作\(例如，从 Service对象中移除此 Pod对象\)以确保不会有客户端请求接入此 Pod 对象 。



```
apiVersion: v1
kind: Pod 
metadata:
  name: Name
spec:
  containers:
  - name: PodName
    image: string 
    readinessProbe:
      exec:
        command: ["test","-e","/usr/share/nginx/html/healhtz"]
      httpGet:
        path: /healthz 
        port: 8080 
        scheme: HTTP 
        httpHeaders:
        - name: string
          value: string 
      tcpSocket:
        port: int
```



