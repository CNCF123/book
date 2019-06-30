```
apiVersion: v1
kind: Pod 
metadata:
  name: Name
  namespace: NameSpace
  labels:
    name: string 
  annotations:
    name: string
spec:
  initContainers:
  - name: busybox
    image: busybox
    command: ["/bin/sh","-c", "sleep 10"]
  containers:
  - name: nginx
    image: nginx 
    imagePullPolicy: [Always | IfNotPresent | Never]
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh","-c","echo lifecycle hooks handler" > /usr/share/nginx/html/test.html]
      preStop:
        exec:
          command: ["/bin/sh","-c","echo 容器终止之前的操作"]
    command: ["/bin/sh"] 
    args: ["-c", "while true; do sleep 30 ; done"] 
    workingDir: /DirName 
    volumeMounts:
    - name: string 
      mountPath:  /DirName
    readOnly: [ture | false]
    ports:
    - name: string
      containerPort: int 
      hostPort: int 
      protocol: [tcp | udp]
    env:
    - name: string
      value: string 
    resources:
      limits:
        cpu:  100m
        memory: 100Mi
      requests:
        cpu: 50m
        memory: 50Mi
    livenessProbe:
      exec:
        command: ["test","-e","/usr/share/nginx/html/healhtz"]
      httpGet:
        path: /healthz 
        port: int 
        host: string 
        scheme: string 
        httpHeaders:
        - name: string
          value: string 
      tcpSocket:
        port: int 
      initialDelaySeconds: 5 
      timeoutSeconds: 1
      periodSeconds: 10 
      successThreshold: 1
      failureThreshold: 3 
    readinessProbe:
      exec:
      httpGet:
      tcpSocket:
    securityContext:
      privileged: false
  restartPolicy: [Always | Never | OnFailure] 
  nodeSelector:
    diskType: ssd
  imagePullSecrets:
  - name: string
  hostNetwork: false
  volumes:
  - name: string
    emptyDir: {} 
    hostPath:
      path: string 
    secret:
      secretName: 
      items:
      - key: string
        path: string
    configMap:
      name: string 
      items:
      - key: string
        path: string
```



