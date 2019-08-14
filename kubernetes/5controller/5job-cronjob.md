```
apiVersion: batch/v1
kind: Job
metadata:
  name: process-item-$ITEM
  labels:
    jobgroup: jobexample
spec:
  template:
    metadata:
      name: jobexample
      labels:
        jobgroup: jobexample
    spec:
      containers:
      - name: c
        image: busybox
        command: ["sh", "-c", "echo Processing item $ITEM && sleep 5"]
      restartPolicy: Never
```



```
apiVersion: batch/v2alpha1 
kind: CronJob 
metadata: 
  name: hello 
spec: 
  schedule: "*/1 * * * *" 
  jobTemplate: 
    spec: 
      template: 
        spec: 
          containers: 
          - name: hello 
            image: busybox 
            args: 
            - /bin/sh 
            - -c 
            - date; echo Hello from the Kubernetes cluster 
          restartPolicy: OnFailure
```





