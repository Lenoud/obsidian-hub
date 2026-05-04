# CronJob 定时任务

使用 Kubernetes CronJob 创建定时执行的任务，支持 cron 表达式调度。

CronJob 名称：cronjob，每分钟打印当前时间。

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: busybox
            image: busybox
            command:
            - /bin/sh
            - -c
            - date
          restartPolicy: OnFailure
```

```bash
kubectl get cronjobs
kubectl get jobs
```
