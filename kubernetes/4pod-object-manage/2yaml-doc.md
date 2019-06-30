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
  containers:
  - name: PodName
    image: string 
    imagePullPolicy: [Always | IfNotPresent | Never]
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
        command: [string]
      httpGet:
        path: string 
        port: int 
        host: string 
        scheme: string 
        httpHeaders:
        - name: string
          value: string 
      tcpSocket:
        port: int 
      initialDelaySeconds: 0 
      timeoutSeconds: 0 
      periodSeconds: 0 
      successThreshold: 0
      failureThreshold: 0
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



