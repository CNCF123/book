```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-myapp 
spec:
  replicas: 1
  selector:
    matchLabels: 
      app: myapp
      env: dev
  template: 
    metadata:
      labels:
        app: myapp 
        env: dev
    spec:
      containers:
      - name: myapp
        image: chenkaidi/myapp:v1 
        ports:
        - name: http
          containerPort: 80
```



