# 评分点
cat api_manager_service.py

「import requests || yaml || json || bearer」命中

python3 api_manager_service.py

「name || resourceVersion || targetPort || port」命中

cat service_api_dev.json

「name || resourceVersion || targetPort || port」命中

```python
import requests,yaml,json,urllib3

urllib3.disable_warnings()

token="bearer eyJhbGciOiJSUzI1NiIsImtpZCI6Im5VMXZuOGRkTUNuLVhTaTRNNHQyVmdGUU1vbVNCR2ZCdUNqWG11QnJMa2sifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGFzaGJvYXJkLWFkbWluIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiNjk5ODI4OGEtYmQ5NS00ODA5LThjODktYjM0OGZlOTM3MmU3Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRhc2hib2FyZC1hZG1pbiJ9.qsq3kDsttJK7Qb_5o_MbCHZ6Lf-q8X_GYjtsxxz3C3GUwdjYp_dP_jqOAEDek0iXxCVRfV7mB2233-LOcn9dyemIcWlYG5nUvDOp5WJaGrzcYzISqf7p98_-JwCTsJzD4OSvc8HJQR-Q7mlSMUHTj1uGp36qs0VNOanIGf8UKO4A2LePBvC6Yvupx0k8K1k9ZzgnhVStGbSNW6vNsSn4_qx7IVUAP3x023EOmWBwGvWqQVb4rvZ9AAiG0sA-b8pMCLBm82en3MrS3Cca_SsCmeJOdsW6m2O2nCbWIlJuqAVsz9zua6vpZSVMXmLkBWprqf1spsuFPuwMkGNzGoOIYg"
headers={
  "Content-Type":"application/json",
  "Authorization": token
}

delete=requests.delete("https://192.168.100.36:6443/api/v1/namespaces/default/services/nginx-svc",headers=headers,verify=False)
print("删除:\n",delete.text)

with open("./service.yaml","r") as f:
  #yaml_file=json.dumps(yaml.safe_load(f.read()))
  yaml_file=json.dumps(yaml.safe_load(f.read()))
create=requests.post("https://192.168.100.36:6443/api/v1/namespaces/default/services/",headers=headers,verify=False,data=yaml_file)
print(create.text)
with open("./service_api_dev.json","w") as f:
  f.write(create.text)

with open("./service_updata.yaml","r") as f:
  up_yaml_file=json.dumps(yaml.safe_load(f.read()))
updata=requests.put("https://192.168.100.36:6443/api/v1/namespaces/default/services/nginx-svc",headers=headers,verify=False,data=up_yaml_file)
#updata=requests.patch("https://192.168.100.36:6443/api/v1/namespaces/default/services/nginx-svc",headers=headers,verify=False,data=up_yaml_file)
print(updata.text)
with open("./service_api_dev.json","a") as f:
  f.write(updata.text)

```
