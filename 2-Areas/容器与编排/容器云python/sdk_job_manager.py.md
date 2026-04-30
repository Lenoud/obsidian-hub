# sdk_job_manager.py

sdk_job_manager.py相关技术笔记。

```python
from kubernetes import client, config
import yaml
import time

# 配置 Kubernetes Python SDK
config.load_kube_config(r"/root/kubeconfig.yaml")

# 指定要创建的 Job 名称
job_name = "pi"

'''这个with open 评分点'''
#with open("file.yaml","r") as file:
# 创建 Job 配置
job_yaml = f'''
apiVersion: batch/v1
kind: Job
metadata:
  name: {job_name}
spec:
  backoffLimit: 4
  template:
    spec:
      containers:
        - name: pi
          image: perl
          command:
            - perl
            - "-Mbignum=bpi"
            - "-wle"
            - "print bpi(2000)"
      restartPolicy: Never
'''

#将字符串转换为相应的 Python 字典或列表对象
job_config=yaml.safe_load(job_yaml)

# 创建 Kubernetes API 客户端
api_instance = client.BatchV1Api()

# 检查是否存在同名的 Job，如果存在则删除

try:
    api_instance.delete_namespaced_job(job_name, "default")
    print("同名job删除成功")
except:
    print("没有同名job")

time.sleep(3)

# 创建新的 Job
job = api_instance.create_namespaced_job("default", body=job_config)
print(job)

```
