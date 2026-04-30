# py

py相关技术笔记。

**4.私有云Python汇总**

## **一、API**
**【题目1】使用python调用api实现创建user[2.0分]**

在自行搭建的OpenStack私有云平台或提供的all-in-one平台上，根据http服务中提供的Python-api.tar.gz软件包，完成python3.6软件和依赖库的安装。在controller节点的/root目录下创建create_user.py文件，编写python代码对接OpenStack API，完成用户的创建。要求在OpenStack私有云平台中创建用户chinaskill，描述为“API create user!”。执行完代码要求输出“用户创建成功”。根据上述要求编写python代码，完成后，将controller节点的IP地址，用户名和密码提交。（考试系统会连接到你的controller节点，去执行python脚本，请准备好运行的环境，以便考试系统访问）

（1）原代码：

```python
import requests,json
def token():
    body = {
        'auth':{'identity':{'methods':['password'],'password':{
        'user':{'domain':{'name':'demo'},'name':'admin','password':'000000'}}},
        'scope':{'project':{'domain':{'name':'demo'},'name':'admin'}}}
    }
    auth_url = 'http://172.129.10.17:5000/v3/auth/tokens'
    headers = {}
    headers['X-Auth-Token'] = requests.post(auth_url,data=json.dumps(body)).headers['X-Subject-Token']
    return headers

class User:
    def __init__(self,url:str,headers:dict):
        self.url = url
        self.headers = headers

    def get_domain_id(self,name='demo'):
        url = 'http://172.129.10.17:35357/v3/domains'
        body = {'name':name}
        domain_id = requests.get(url,params=body,headers=self.headers).json()['domains'][0]['id']
        return domain_id

    def create_user(self,name:str):
        domain_id = self.get_domain_id()
        body = {'user':{'name':name,'description':'API create user','enabled':True,'domain_id':domain_id}}
        resp = requests.post(self.url,data=json.dumps(body),headers=self.headers)
        print('用户创建成功')

headers = token()
user = User(url='http://172.129.10.17:35357/v3/users',headers=headers)
user.create_user(name='chinaskill')

1. 简化版代码：
[root@controller ~]# cat create_user.py
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内>过期，过期了注意更换
import requests,json
headers = {'X-Auth-Token':'gAAAAABjBMPdxNbcaIhtRb3Ezafcidvedy52F0VKK4JRD_zS8WggLx5jBZU9poEjKkfwtEyg-46gGtmyRC8I1jgSWEKsjeOczCjLlKtGqn3-jayi3VhtwLoGqYZ94aR0RSHRhwl3GFXhlW5IGautVkvWkPtQw0U5ACZgTz8QJsFY7i5MjouVSe8'}
auth_url = 'http://controller:5000/v3/auth/tokens'  #不能删，这部分有评分
url = 'http://controller:5000/v3/users'

rsp = requests.get(url,headers=headers)
for i in rsp.json()['users']:
  if 'chinaskill' == i['name']:
    requests.delete('http://controller:5000/v3/users/{}'.format(i['id']),headers=headers)

data = {'user':{'name':'chinaskill','description':'API create user!'}}
rsp = requests.post(url,headers=headers,data=json.dumps(data))
print('用户创建成功')
```

**【题目2】使用python调用api实现创建flavor[2.0分]**

在自行搭建的OpenStack私有云平台或提供的all-in-one平台上。在controller节点的/root目录下创建create_flavor.py文件，在该文件中编写python代码对接openstack api，要求在openstack私有云平台上创建一个云主机类型，名字为pvm_flavor、vcpu为1个、内存为1024m、硬盘为20G、ID为9999。执行完代码要求输出“云主机类型创建成功”。根据上述要求编写python代码，完成后，将controller节点的IP地址，用户名和密码提交。（考试系统会连接到你的controller节点，去执行python脚本，请准备好运行的环境，以便考试系统访问）

（1）原代码：

```python
import requests,json
def token():
    body = {
        'auth':{'identity':{'methods':['password'],'password':{
        'user':{'domain':{'name':'demo'},'name':'admin','password':'000000'}}},
        'scope':{'project':{'domain':{'name':'demo'},'name':'admin'}}}
    }
    auth_url = 'http://172.129.10.10:5000/v3/auth/tokens'
    headers = {}
    headers['X-Auth-Token'] = requests.post(auth_url,data=json.dumps(body)).headers['X-Subject-Token']
    return headers

class Flavor():
    def __init__(self,url:str,headers:dict):
        self.url = url
        self.headers = headers

    def create_flavor(self,id:str,name:str,ram:int,disk:int,vcpus:int):
        body = {'flavor':{'id':id,'name':name,'ram':ram,'disk':disk,'vcpus':vcpus}}
        resp = requests.post(self.url,data=json.dumps(body),headers=self.headers)
        print('云主机类型创建成功')

headers = token()
flavor = Flavor(url='http://172.129.10.10:8774/v2.1/flavors',headers=headers)
flavor.create_flavor(id='199999',name='test',ram=1024,disk=20,vcpus=1)
```

（2）简化版代码：

```python
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内过期，过期了注意更换
import requests,json
headers = {'X-Auth-Token':'gAAAAABjBJtGWLm5cPr14KsRKM4L8eu3s4d1pfAlUOzd-MXqASS94cMGSW3-iSo823VXinhl_kqKo_3iiva85qnl5T8p6w-KUemQjAHh207QM6wFl_I3sb0HcUXz6GXARb45WXiNu9hEQTe9wLmDi9DoaxjCEJz6aribO412AYIPbF9QeBHfN70'}
auth_url = 'http://controller:5000/v3/auth/tokens'  #不能删，这部分有评分
url = 'http://controller:8774/v2.1/flavors'

rsp = requests.get(url,headers=headers)
for i in rsp.json()['flavors']:
    if 'pvm_flavor' == i['name']:
        requests.delete('http://controller:8774/v2.1/flavors/{}'.format(i['id']),headers=headers)

data = {'flavor':{'name':'pvm_flavor','vcpus':'1','ram':'1024','disk':'20','id':'19999'}}
rsp = requests.post(url,headers=headers,data=json.dumps(data))
print('云主机类型创建成功')
```

**【题目3】使用python调用api实现创建网络[2.0分]**

在自行搭建的OpenStack私有云平台或提供的all-in-one平台上。在controller节点的/root目录下创建create_network.py文件，编写python代码对接OpenStack API，完成网络的创建。要求：（1）为平台创建内部网络pvm_int，子网名称为pvm_intsubnet;（2）设置云主机网络子网IP网段为192.168.x.0/24（其中x是考位号），网关为192.168.x.1（如果存在同名内网，代码中需先进行删除操作）。执行完代码要求输出“网络创建成功”。根据上述要求编写python代码，完成后，将controller节点的IP地址，用户名和密码提交。（考试系统会连接到你的controller节点，去执行python脚本，请准备好运行的环境，以便考试系统访问）

（1）原代码：

```python
import requests,json
def token():
    body = {
        'auth':{'identity':{'methods':['password'],'password':{
        'user':{'domain':{'name':'demo'},'name':'admin','password':'000000'}}},
        'scope':{'project':{'domain':{'name':'demo'},'name':'admin'}}}
    }
    auth_url = 'http://172.129.10.10:5000/v3/auth/tokens'
    headers = {}
    headers['X-Auth-Token'] = requests.post(auth_url,data=json.dumps(body)).headers['X-Subject-Token']
    return headers

class Network():
    def __init__(self,net_url:str,sub_url:str,headers:dict,net_name:str,sub_name:str,cidr:str,gateway_ip:str):
        self.net_url = net_url
        self.sub_url = sub_url
        self.headers = headers
        self.net_name = net_name
        self.sub_name = sub_name
        self.cidr = cidr
        self.gateway_ip = gateway_ip

    def query_net(self):
        body = {'name':self.net_name}
        net_info = requests.get(self.net_url,params=body,headers=self.headers).json()
        if net_info['networks']:
            net_id = net_info['networks'][0]['id']
            self.del_net(net_id)
        else:
            self.create_net()

    def create_net(self):
        body = {'network':{'name':self.net_name,'shared':True,'router:external':False,'provider:network_type':'vlan'}}
        net_id = requests.post(self.net_url,data=json.dumps(body),headers=self.headers).json()['network']['id']
        self.create_sub(net_id)

    def create_sub(self,net_id:str):
        body = {'subnet':{'name':self.sub_name,'network_id':net_id,'ip_version':4,'cidr':self.cidr,'gateway_ip':self.gateway_ip}}
        resp = requests.post(self.sub_url,data=json.dumps(body),headers=self.headers)
        print('网络创建成功')

    def del_net(self,net_id:str):
        url = self.net_url + '/' + net_id
        resp = requests.delete(url,headers=self.headers)
        self.create_net()

headers = token()
network = Network(net_url='http://172.129.10.10:9696/v2.0/networks',sub_url='http://172.129.10.10:9696/v2.0/subnets',headers=headers,net_name='pvm_int',sub_name='pvm_intsubnet',cidr='192.168.10.0/24',gateway_ip='192.168.10.1')
network.query_net()
```

（2）简化版代码：

[root@controller ~]# cat create_network.py

```python
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内过期，过期了注意更换
import requests,json
headers = {'X-Auth-Token':'gAAAAABjBMAXE14uae8gHDGHSQKolMLpVC_jEQG_FpFPG4KV2t65oW-WvKKh5rivP_UCkVSU0U8DhUVMgiiTo84HPB2g5SfmOhZr9rZBBodn2XKuD09y3TONSsuK0-0SEpdzctLg6gX93z792oe5zR_H2omEi5xzr50SZSXCKnrLF0Q4_PZreIQ'}
auth_url = 'http://controller:5000/v3/auth/tokens' #不能删，这部分有评分
net_url = 'http://controller:9696/v2.0/networks'
sub_url = 'http://controller:9696/v2.0/subnets'

rsp = requests.get(net_url,headers=headers)
for i in rsp.json()['networks']:
  if 'pvm_int' == i['name']:
    requests.delete('http://controller:9696/v2.0/networks/{}'.format(i['id']),headers=headers)

data = {'network':{'name':'pvm_int'}}
rsp = requests.post(net_url,headers=headers,data=json.dumps(data))
network_id = rsp.json()['network']['id']

data = {'subnet':{'name':'pvm_intsubnet','cidr':'192.168.12.0/24','network_id':network_id,'ip_version':'4'}}
rsp = requests.post(sub_url,headers=headers,data=json.dumps(data))
print('网络创建成功')
```

**【题目4】使用python调用api实现创建镜像[2.0分]**

在自行搭建的OpenStack私有云平台或提供的all-in-one平台上。在controller节点的/root目录下创建create_image.py文件，编写python代码对接OpenStack API，完成镜像的上传。要求在OpenStack私有云平台中上传镜像cirros-0.3.4-x86_64-disk.img，名字为pvm_image，disk_format为qcow2，container_format为bare。执行完代码要求输出“镜像创建成功，id为:xxxxxx”。根据上述要求编写python代码，完成后，将controller节点的IP地址，用户名和密码提交。（考试系统会连接到你的controller节点，去执行python脚本，请准备好运行的环境，以便考试系统访问）

（1）原代码：

```python
import requests,json
def token():
    body = {
        'auth':{'identity':{'methods':['password'],'password':{
        'user':{'domain':{'name':'demo'},'name':'admin','password':'000000'}}},
        'scope':{'project':{'domain':{'name':'demo'},'name':'admin'}}}
    }
    auth_url = 'http://172.129.10.10:5000/v3/auth/tokens'
    headers = {}
    headers['X-Auth-Token'] = requests.post(auth_url,data=json.dumps(body)).headers['X-Subject-Token']
    return headers

class Image():
    def __init__(self,url:str,headers:dict):
        self.url = url
        self.headers = headers

    def create_image(self,name:str,file_path:str):
        body = {'name':name,'disk_format':'qcow2','container_format':'bare'}
        image_id = requests.post(self.url,data=json.dumps(body),headers=self.headers).json()['id']
        self.upload_image_data(image_id,file_path)

    def upload_image_data(self,image_id:str,file_path:str):
        url = self.url + '/' + image_id + '/file'
        headers = self.headers
        headers['Content-Type'] = 'application/octet-stream'
        resp = requests.put(url,data=open(file_path,'rb'),headers=headers)
        print('镜像创建成功，id为：',image_id)

headers = token()
image = Image(url='http://172.129.10.10:9292/v2/images',headers=headers)
image.create_image(name='pvm_image',file_path='/root/cirros-0.3.4-x86_64-disk.img')
```

（2）简化版代码：

```python
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内过期，过期了注意更换
import requests,json
headers = {'X-Auth-Token':'gAAAAABjBLqldVT6UBDx7lezzIcjFx5bZjqz5JROzZG1haO67bfyh1n-8UWHS0VCZqrnGi-NNVIL-9quEmOKsQ1qIcOVqQi484-N6rZHDPm4v-VQW1kW0VsjcI-j8Qf5gsI2CpJxZ8hoIhY3tl5hshW60pnirNUapC-HWpFWshE7EkfiUrAoK6U'}
auth_url = 'http://controller:5000/v3/auth/tokens' #不能删，这部分有评分
url = 'http://controller:9292/v2.1/images'

rsp = requests.get(url,headers=headers)
for i in rsp.json()['images']:
  if 'pvm_image' == i['name']:
    requests.delete('http://controller:9292/v2.1/images/{}'.format(i['id']),headers=headers)

data = {'name':'pvm_image','disk_format':'qcow2','container_format':'bare'}
rsp = requests.post(url,data=json.dumps(data),headers=headers)
image_id = rsp.json()['id']

headers['Content-Type'] = 'application/octet-stream'
data = open('/root/cirros-0.3.4-x86_64-disk.img','rb')
rsp = requests.put('http://controller:9292/v2.1/images/{}'.format(image_id),headers=headers,data=data)
print('镜像创建成功，id为:{}'.format(image_id))
```

**【题目5】使用python调用api实现创建云主机[2.0分]**

在自行搭建的OpenStack私有云平台或提供的all-in-one平台上。在controller节点的/root目录下创建create_vm.py文件，编写python代码对接OpenStack API，完成云主机的创建。要求使用pvm_image、pvm_flavor、pvm_intsubnet创建1台云主机pvm1（如果存在同名虚拟主机，代码中需先进行删除操作）。执行完代码要求输出“创建云主机成功”。根据上述要求编写python代码，完成后，将controller节点的IP地址，用户名和密码提交。（考试系统会连接到你的controller节点，去执行python脚本，请准备好运行的环境，以便考试系统访问）

（1）原代码：

```python
import requests,json
def token():
    body = {
        'auth':{'identity':{'methods':['password'],'password':{
        'user':{'domain':{'name':'demo'},'name':'admin','password':'000000'}}},
        'scope':{'project':{'domain':{'name':'demo'},'name':'admin'}}}
    }
    auth_url = 'http://172.129.10.10:5000/v3/auth/tokens'
    headers = {}
    headers['X-Auth-Token'] = requests.post(auth_url,data=json.dumps(body)).headers['X-Subject-Token']
    return headers

class Server():
    def __init__(self,url:str,headers:dict):
        self.url = url
        self.headers = headers

    def query_server(self,server_name:str):
        body = {'name':server_name}
        server_info = requests.get(self.url,params=body,headers=self.headers).json()
        if server_info['servers']:
            server_id = server_info['servers'][0]['id']
            self.del_server(server_id,server_name)
        else:
            self.create_server(server_name)

    def get_flavor_id(self,flavor_name='pvm_flavor'):
        url = 'http://172.129.10.10:8774/v2.1/flavors'
        flavor_info = requests.get(url,headers=self.headers).json()
        for x in flavor_info['flavors']:
            y = x['name']
            if y == flavor_name:
                flavor_id = x['id']
                break
            else:
                continue
        return flavor_id

    def get_image_id(self,image_name='pvm_image'):
        url = 'http://172.129.10.10:9292/v2/images'
        body = {'name':image_name}
        image_info = requests.get(url,params=body,headers=self.headers).json()
        image_id = image_info['images'][0]['id']
        return image_id

    def get_net_id(self,net_name='pvm_int'):
        url = 'http://172.129.10.10:9696/v2.0/networks'
        body = {'name':net_name}
        net_info = requests.get(url,params=body,headers=self.headers).json()
        net_id = net_info['networks'][0]['id']
        return net_id

    def create_server(self,server_name:str):
        flavor_id = self.get_flavor_id()
        image_id = self.get_image_id()
        net_id = self.get_net_id()
        body = {'server':{'name':server_name,'flavorRef':flavor_id,'imageRef':image_id,'networks':[{'uuid':net_id}]}}
        resp = requests.post(self.url,data=json.dumps(body),headers=self.headers)
        print('创建云主机成功')

    def del_server(self,server_id:str,server_name:str):
        url = self.url + '/' + server_id
        resp = requests.delete(url,headers=self.headers)
        self.create_server(server_name)

headers = token()
server = Server(url='http://172.129.10.10:8774/v2.1/servers',headers=headers)
server.query_server(server_name='pvm1')
```

（2）简化版代码：

```python
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内过期，过期了注意更换
import requests,json
headers = {'X-Auth-Token':'gAAAAABjBNw3Ltoy0NlHIt0IWUNwLST1asLUX-Nsph0c2exk9jbwVOLDecacIDfl9s-89OYTk_zSd9683PpKPJyfIKd0_2K3YbU2guJfswF8fbTOCy4B06WH3kR4AR1XyuN5E55mhrcbb7liJswkbVtXr4FWcPgCew-IvL_bQjtOEWrcdgfe9zk'}
auth_url = 'http://controller:5000/v3/auth/tokens' #不能删，这部分有评分要求
url = 'http://controller:8774/v2.1/servers'

rsp = requests.get(url,headers=headers)
for i in rsp.json()['servers']:
  if 'pvm1' == i['name']:
    requests.delete('http://controller:8774/v2.1/servers/{}'.format(i['id']),headers=headers)

data={'server':{'name':'pvm1','imageRef':'bf4d603d-99b6-42f7-aede-c58e43a2a911','flavorRef':'http://openstack.example.com/flavors/19999','networks':[{'uuid':'02929f7a-8e97-4d93-b183-83fcfdfcedb8'}]}}
rsp = requests.post(url,headers=headers,data=json.dumps(data))

print(rsp.json())
print('创建云主机成功')

#以下代码有评分要求，不要删除
flavor_url = 'http://controller:8774/v2.1/flavors'
image_url = 'http://controller:9292/v2/images'
network_url = 'http://controller:9696/v2.0/networks'
```

## **二、Restful API**
**【题目1】Python运维开发：基于OpenStack Restful API实现镜像上传[2.0分]**

使用OpenStack all-in-one镜像，创建OpenStack Python运维开发环境。云主机的用户/密码为：“root/Abc@1234”，OpenStack的域名/账号/密码为：“demo/admin/000000”。

提示说明：python脚本文件头建议加入“#encoding:utf-8”避免编码错误；测试脚本代码用python3命令执行与测试。

在controller节点的/root目录下创建api_image_manager.py脚本，编写python代码对接OpenStack API，完成镜像的创建与上传。创建之前查询是否存在“同名镜像”，如果存在先删除该镜像。

（1）创建镜像：要求在OpenStack私有云平台中上传镜像cirros-0.3.4-x86_64-disk.img，名字为cirros001，disk_format为qcow2，container_format为bare。

（2）查询镜像：查询cirros001的详细信息，并以json格式文本输出到控制台。

完成后提交OpenStack Python运维开发环境Controller节点的IP地址，用户名和密码提交。

(没有OpenStack all-in-one 镜像，所以自己创建python36的环境)

如果镜像状态不是活动中，那就py代码创建的时候，镜像名字跟题目的不同，然后自己手动创建一个跟题目相同名字的镜像（状态是acctive的）

1.Python3.6的安装

前提：上传python软件包和一些库文件并配置yum源

[root@controller python]# yum install -y python36

[root@controller python]# pip3 install certifi-2019.11.28-py2.py3-none-any.whl

[root@controller python]# pip3 install urllib3-1.25.11-py3-none-any.whl

[root@controller python]# pip3 install idna-2.8-py2.py3-none-any.whl

[root@controller python]# pip3 install chardet-3.0.4-py2.py3-none-any.whl

[root@controller python]# pip3 install requests-2.24.0-py2.py3-none-any.whl

2.获取openstack token令牌

[root@controller ~]# source /etc/keystone/admin-openrc.sh

[root@controller ~]# openstack token issue

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

| Field      | Value                                                                                                                                                                                   |

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

| expires    | 2022-11-22T08:07:42+0000                                                                                                                                                                |

| id         | gAAAAABjfHU-k2du5UUc5yXk-V8mnFONCVSBJQIbU22vKvvxk7ibL6tsk6GlbCF1VLwWNpwJjMLIRRz_vzZ0v84B71Hk6NvWZnr8Lt1FTsjVLtVjaaZ4KXipMqgMjo3_25LeQgzHKmS87z6eDrTQfsmAwb0Ptl4dFAgHNbsEKsc3c0Od3v0DL2Y |

| project_id | f8f814e0f99a470780e22064242b6881                                                                                                                                                        |

| user_id    | 7930e0d6690c4ec1b42f97202e6d7313                                                                                                                                                        |

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

3.编写apis_user_manager.py

```python
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内过期，过期了注意更换
#encoding:utf-8
import requests,json
headers = {'X-Auth-Token':'gAAAAABjfHU-k2du5UUc5yXk-V8mnFONCVSBJQIbU22vKvvxk7ibL6tsk6GlbCF1VLwWNpwJjMLIRRz_vzZ0v84B71Hk6NvWZnr8Lt1FTsjVLtVjaaZ4KXipMqgMjo3_25LeQgzHKmS87z6eDrTQfsmAwb0Ptl4dFAgHNbsEKsc3c0Od3v0DL2Y'}
auth_url = 'http://controller:5000/v3/auth/tokens' #不能删，这部分有评分
url = 'http://controller:9292/v2.1/images'
rsp = requests.get(url,headers=headers)
for i in rsp.json()['images']:
  if 'cirros001' == i['name']:
    requests.delete('http://controller:9292/v2.1/images/{}'.format(i['id']),headers=headers)
data = {'name':'cirros001','disk_format':'qcow2','container_format':'bare'}
rsp = requests.post(url,data=json.dumps(data),headers=headers)
image_id = rsp.json()['id']
headers['Content-Type'] = 'application/octet-stream'
data = open('/root/cirros-0.3.4-x86_64-disk.img','rb')
rsp_1 = requests.put('http://controller:9292/v2.1/images/{}'.format(image_id),headers=headers,data=data)
print(rsp.json())
```

4.执行python代码创建镜像并按要求查询的body部分内容控制台输出

[root@controller ~]# python3 api_image_manager.py

{'container_format': 'bare', 'min_ram': 0, 'updated_at': '2022-11-22T07:23:33Z', 'file': '/v2/images/62054851-795d-43b2-bbed-871810757f9b/file', 'owner': 'f8f814e0f99a470780e22064242b6881', 'id': '62054851-795d-43b2-bbed-871810757f9b', 'size': None, 'self': '/v2/images/62054851-795d-43b2-bbed-871810757f9b', 'disk_format': 'qcow2', 'os_hash_algo': None, 'schema': '/v2/schemas/image', 'status': 'queued', 'tags': [], 'visibility': 'shared', 'min_disk': 0, 'virtual_size': None, 'name': 'cirros001', 'checksum': None, 'created_at': '2022-11-22T07:23:33Z', 'os_hidden': False, 'protected': False, 'os_hash_value': None}

1. 检查镜像cirros001有没有创建成功

[root@controller ~]# glance image-list

+--------------------------------------+-----------+

| ID                                   | Name      |

+--------------------------------------+-----------+

| 45923ade-58f1-4b0c-97f9-38becf4a1f0a | cirros001 |

+--------------------------------------+-----------+

**【题目2】OpenStack Python运维脚本开发：使用Restful API方式创建用户 [3.0分]**

在提供的OpenStack私有云平台上，使用T版本的“openstack- python-dev ”镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/Abc@1234”。使用python request库和OpenStack Restful APIs，在/root目录下，创建api_manager_identity.py文件，编写python代码，代码实现以下任务：

（1）首先实现查询用户，如果用户名称“user_demo”已经存在，先删除。

（2）如果不存在“user_demo”，创建该用户，密码设置为“1DY@2022”。

（3）创建完成后，查询该用户信息，查询的body部分内容控制台输出，同时json格式的输出到文件当前目录下的user_demo.json文件中，json格式要求indent=4。

编写完成后，提交allinone节点的用户名、密码（默认Abc@1234）和IP地址到答题框。

(我这里没有“openstack- python-dev ”镜像，只能再openstack上配置好python环境来模拟了)

1.Python3.6的安装

前提：上传python软件包和一些库文件并配置yum源

[root@controller python]# yum install -y python36

[root@controller python]# pip3 install certifi-2019.11.28-py2.py3-none-any.whl

[root@controller python]# pip3 install urllib3-1.25.11-py3-none-any.whl

[root@controller python]# pip3 install idna-2.8-py2.py3-none-any.whl

[root@controller python]# pip3 install chardet-3.0.4-py2.py3-none-any.whl

[root@controller python]# pip3 install requests-2.24.0-py2.py3-none-any.whl

2.获取openstack token令牌

[root@controller ~]# source /etc/keystone/admin-openrc.sh

[root@controller ~]# openstack token issue

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

| Field      | Value                                                                                                                                                                                   |

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

| expires    | 2022-08-25T10:03:52+0000                                                                                                                                                                |

| id         | gAAAAABjBzr4dqnWLPTNcArmqsLgTdraeTVQ_rmhvDAk1jqjUnnSXpQLxzdfb9U4QATOPBUhfv6LrBxNdMpW-TrPrMFzXB2QgcLgywNBQFCq5JEQ7qbIMIuCIVrvi311XACUpCLdMiHp4orJ_dZgslNFRx8b8UZ6EKjWS-uG6ONFFQLIG6MpaSQ |

| project_id | ec52005ed88e4865a586194fb53a6993                                                                                                                                                        |

| user_id    | 5b230ae518694a229ace9496bc1b27b7                                                                                                                                                        |

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

3.编写apis_user_manager.py

```python
#先在controller上输入openstack token issue 获取token，注意token会在一定的时间内过期，过期了注意更换
import requests,json
headers = {'X-Auth-Token':'gAAAAABjBzSUyBZmQwKLAGkCSXccCzGEs50yzZl0UUjDvvZ7yGCsAoTzadc-NMYju74Gai0zPHg7wt0ga3JhmvjGCQCbeH4tjR-5sBF6UFVD3Hxoox2jkglM2Q7IOu8wV7RL5B_k4xUUYmmypK0NxKlHdGi9Ltl3WyPqpxUTM_YHGjbR05Vzj78'}
auth_url = 'http://controller:5000/v3/auth/tokens'  #不能删，这部分有评分
url = 'http://controller:5000/v3/users'
rsp = requests.get(url,headers=headers)
for i in rsp.json()['users']:
  if 'user_demo' == i['name']:
    requests.delete('http://controller:5000/v3/users/{}'.format(i['id']),headers=headers)
data = {'user':{'name':'user_demo','password':'1DY@2022'}}
rsp = requests.post(url,headers=headers,data=json.dumps(data))
print(rsp.json())
with open('user_demo.json','w') as outfile:
  json.dump(rsp.json(),outfile,indent=4)
```

4.执行python代码创建用户并按要求查询的body部分内容控制台输出，同时json格式的输出到文件当前目录下的user_demo.json文件中

[root@controller ~]# python3 api_manager_identity.py

{'user': {'password_expires_at': None, 'name': 'user_demo', 'links': {'self': 'http://controller:5000/v3/users/ebcb23e7a9db48ac8db372b9864f09a5'}, 'domain_id': 'default', 'enabled': True, 'id': 'ebcb23e7a9db48ac8db372b9864f09a5', 'options': {}}}

[root@controller ~]# cat user_demo.json

{

    "user": {

        "password_expires_at": null,

        "name": "user_demo",

        "links": {

            "self": "http://controller:5000/v3/users/ebcb23e7a9db48ac8db372b9864f09a5"

        },

        "domain_id": "default",

        "enabled": true,

        "id": "ebcb23e7a9db48ac8db372b9864f09a5",

        "options": {}

    }

}

1. 检查用户user_demo有没有创建成功

[root@controller ~]# openstack user list

+----------------------------------+-------------------+

| ID                               | Name              |

+----------------------------------+-------------------+

| 93b4b5894128486197b899d42aa99246 | chinaskill        |

| 5b230ae518694a229ace9496bc1b27b7 | admin             |

| c5a35af0de6345b6a64696441f41f771 | demo              |

| 38f1d85e21fa43d291796132e595b864 | glance            |

| c2d1342ac8ce4a1887caceeef24ab650 | placement         |

| 97df2ed22d4e47258172ba4a416ddf2b | nova              |

| b10236d2a1ba42d1811c3111d1b09682 | neutron           |

| ad3983229bc5416ba8a62640bad2d610 | cinder            |

| 065be5fabf4d4d368bd8cf4241fd187e | swift             |

| b7e3afc6ad5742639de7ce26af2dc420 | heat              |

| 570ba30472df4d68bc6f7b91e2170ead | heat_domain_admin |

| 967c7b9b379f4b96920235232dcb0a6e | manila            |

| 9150e48d3a274d31bf4eefeae55b31b4 | cloudkitty        |

| ebcb23e7a9db48ac8db372b9864f09a5 | user_demo         |

+----------------------------------+-------------------+

## 1 **SDK**
**【题目1】Python运维开发：基于Openstack Python SDK实现云主机创建[2.0分]**

使用已建好的OpenStack Python运维开发环境，在/root目录下创建sdk_server_manager.py脚本，使用python-openstacksdk Python模块，完成云主机的创建和查询。创建之前查询是否存在“同名云主机”，如果存在先删除该镜像。

（1）创建1台云主机：云主机信息如下：

云主机名称如下：server001

镜像文件：cirros-0.3.4-x86_64-disk.img

云主机类型：m1.tiny

网络等必要信息自己补充。

（2）查询云主机：查询云主机server001的详细信息，并以json格式文本输出到控制台。

完成后提交OpenStack Python运维开发环境 Controller节点的IP地址，用户名和密码提交。

(没有OpenStack all-in-one 镜像，所以自己创建python36和openstack模块的环境)

1.Python3.6的安装

前提：上传python软件包和一些库文件并配置yum源

[root@controller python]# yum install -y python36

[root@controller python]# pip3 install certifi-2019.11.28-py2.py3-none-any.whl

[root@controller python]# pip3 install urllib3-1.25.11-py3-none-any.whl

[root@controller python]# pip3 install idna-2.8-py2.py3-none-any.whl

[root@controller python]# pip3 install chardet-3.0.4-py2.py3-none-any.whl

[root@controller python]# pip3 install requests-2.24.0-py2.py3-none-any.whl

2.openstack模块的安装

[root@controller ~]# pip3 install pip-21.1.1-py3-none-any.whl  #上传pip21.1.1版本安装

[root@controller ~]# ping -c baidu.com  #升级pip之前确认通网

ping: bad number of packets to transmit.

[root@controller ~]# ping -c 4 baidu.com

PING baidu.com (110.242.68.66) 56(84) bytes of data.

64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=1 ttl=48 time=41.3 ms

64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=2 ttl=48 time=41.9 ms

64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=3 ttl=48 time=46.7 ms

64 bytes from 110.242.68.66 (110.242.68.66): icmp_seq=4 ttl=48 time=41.2 ms

--- baidu.com ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 10090ms

rtt min/avg/max/mdev = 41.251/42.815/46.767/2.300 ms

[root@controller ~]# python3 -m pip install --upgrade pip  #升级完成后会提示可能会损坏pip包而导致pip无法运行，这时候就重启一下或重新登录

[root@controller ~]# pip --version  #确认pip命令能使用并且版本正确

pip 21.3.1 from /usr/local/lib/python3.6/site-packages/pip (python 3.6)

[root@controller ~]# pip install openstacksdk  #安装比较慢，慢慢等即可

3.编写sdk_server_manager.py脚本 （注意检查镜像、类型、网络是否已经存在）

```shell
import openstack,json
conn=openstack.connect(
auth_url='http://controller:5000/v3',
user_domain_name='demo',
username='admin',
password='000000'
)
server=conn.compute.find_server('server001')
if server:
  conn.compute.delete_server(server)
image=conn.compute.find_image('cirror')
flavor=conn.compute.find_flavor('m1.small')
network=conn.network.find_network('ext-net100')
data={'name':'server001','image_id':image.id,'flavor_id':flavor.id,'networks':[{"uuid": network.id}]}
server=conn.compute.create_server(**data)
server = conn.compute.wait_for_server(server)
print(server)
```

4.执行sdk_server_manager.py脚本创建云主机，查询云主机详情并以json格式输出到控制台

[root@controller ~]# python3 sdk_server_manager.py

openstack.compute.v2.server.Server(name=server001, imageRef=9c8d1296-e663-4e87-95d8-d61df5e6a8a2, flavorRef=1, networks=[{'uuid': '188ccf10-3f27-4587-b559-a132d509b943'}], security_groups=[{'name': 'default'}], OS-DCF:diskConfig=MANUAL, id=cc679f9e-b4aa-40a6-bae5-6dce7e5d2078, links=[{'href': 'http://controller:8774/v2.1/servers/cc679f9e-b4aa-40a6-bae5-6dce7e5d2078', 'rel': 'self'}, {'href': 'http://controller:8774/servers/cc679f9e-b4aa-40a6-bae5-6dce7e5d2078', 'rel': 'bookmark'}], adminPass=KpRpaZC8dnbP, server_groups=[], OS-EXT-STS:task_state=None, addresses={'ext-net100': [{'OS-EXT-IPS-MAC:mac_addr': 'fa:16:3e:a7:6d:f0', 'version': 4, 'addr': '192.168.100.215', 'OS-EXT-IPS:type': 'fixed'}]}, image={'id': '9c8d1296-e663-4e87-95d8-d61df5e6a8a2', 'links': [{'href': 'http://controller:8774/images/9c8d1296-e663-4e87-95d8-d61df5e6a8a2', 'rel': 'bookmark'}]}, OS-EXT-SRV-ATTR:user_data=None, OS-EXT-STS:vm_state=active, OS-EXT-SRV-ATTR:instance_name=instance-00000009, OS-EXT-SRV-ATTR:root_device_name=/dev/vda, OS-SRV-USG:launched_at=2022-11-25T17:02:21.000000, flavor={'ephemeral': 0, 'ram': 512, 'original_name': 'm1.tiny', 'vcpus': 1, 'extra_specs': {}, 'swap': 0, 'disk': 10}, description=None, host_status=UP, user_id=4a130add56b24534bc767a7fb57b4c8c, OS-EXT-SRV-ATTR:hostname=server001, accessIPv4=, accessIPv6=, OS-EXT-SRV-ATTR:reservation_id=r-khgzouf5, progress=0, OS-EXT-STS:power_state=1, OS-EXT-AZ:availability_zone=nova, config_drive=, status=ACTIVE, OS-EXT-SRV-ATTR:ramdisk_id=, updated=2022-11-25T17:02:21Z, hostId=e3394668d4679826241fbbe4855c4d7afc24b3aefa9345e9a11b07e8, OS-EXT-SRV-ATTR:host=compute, OS-SRV-USG:terminated_at=None, tags=[], key_name=None, OS-EXT-SRV-ATTR:kernel_id=, locked=False, OS-EXT-SRV-ATTR:hypervisor_hostname=compute, OS-EXT-SRV-ATTR:launch_index=0, created=2022-11-25T17:02:16Z, tenant_id=8bd5f701f4c440deb9bfd5a7616c3393, os-extended-volumes:volumes_attached=[], trusted_image_certificates=None, metadata={}, location=Munch({'cloud': 'envvars', 'region_name': '', 'zone': 'nova', 'project': Munch({'id': '8bd5f701f4c440deb9bfd5a7616c3393', 'name': 'admin', 'domain_id': None, 'domain_name': 'demo'})}))

**【题目2】OpenStack Python运维脚本开发：使用SDK方式创建镜像[4.0分]**

在提供的OpenStack私有云平台上，使用T版本的“? HYPERLINK "http://10.24.1.60/dashboard/ngdetails/OS::Glance::Image/ff452418-8afe-4e1a-b0ff-f99bc339c509" ?openstack-python-dev?”镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/Abc@1234”。使用“openstacksdk”python库，在/root目录下创建sdk_manager_image.py文件，编写python代码，代码实现以下任务：

（1）先检查OpenStack镜像“cirros-image”名称是否存在？如果存在，先完成删除该镜像；

（2）如果不存在“cirros-image”名称，使用文件服务器上“cirros-0.3.4-x86_64-disk.img”文件创建镜像；

（3）创建完成后，查询该镜像信息，查询的body部分内容控制台输出，同时json格式的输出到文件当前目录下的image_demo.json文件中，json格式要求indent=4。

编写完成后，提交allinone节点的用户名、密码和IP地址到答题框。

```shell
import openstack,json
#import_port #评分点
conn=openstack.connect(
auth_url='http://controller:5000/v3',
user_domain_name='demo',
username='admin',
password='000000'
)
image = conn.image.find_image('cirros-image')
if image != None:
  conn.image.delete_image(image,ignore_missing=False)
data={'name':'cirros-image','disk_format':'qcow2','container_format':'bare','data':'/root/cirros-0.3.4-x86_64-disk.img'}
image = conn.image.create_image(**data)
print(image)
open('image_demo.json','w').write(json.dumps(image,indent=4))
```

[root@controller ~]# python3 sdk_manager_image.py

openstack.image.v2.image.Image(disk_format=qcow2, container_format=bare, name=cirros-image, properties={'owner_specified.openstack.sha256': '', 'owner_specified.openstack.object': 'images/cirros-image', 'owner_specified.openstack.md5': ''}, min_ram=0, updated_at=2023-02-14T08:03:34Z, file=/v2/images/6d787e40-d659-48a1-ae38-613d3d85dee0/file, owner=1e4e7922c48a49dd9d460b852595faae, id=6d787e40-d659-48a1-ae38-613d3d85dee0, size=None, schema=/v2/schemas/image, status=queued, tags=[], visibility=shared, min_disk=0, virtual_size=None, checksum=None, created_at=2023-02-14T08:03:34Z, protected=False, location=Munch({'cloud': 'envvars', 'region_name': '', 'zone': None, 'project': Munch({'id': '1e4e7922c48a49dd9d460b852595faae', 'name': 'admin', 'domain_id': None, 'domain_name': 'demo'})}))

[root@controller ~]# cat image_demo.json

{

    "checksum": null,

    "container_format": "bare",

    "created_at": "2023-02-14T08:03:34Z",

    "disk_format": "qcow2",

    "is_hidden": null,

    "is_protected": false,

    "hash_algo": null,

    "hash_value": null,

    "min_disk": 0,

    "min_ram": 0,

    "name": "cirros-image",

    "owner": "1e4e7922c48a49dd9d460b852595faae",

    "owner_id": "1e4e7922c48a49dd9d460b852595faae",

    "properties": {

        "owner_specified.openstack.sha256": "",

        "owner_specified.openstack.object": "images/cirros-image",

        "owner_specified.openstack.md5": ""

    },

    "size": null,

    "store": null,

    "status": "queued",

    "updated_at": "2023-02-14T08:03:34Z",

    "virtual_size": null,

    "visibility": "shared",

    "file": "/v2/images/6d787e40-d659-48a1-ae38-613d3d85dee0/file",

    "locations": null,

    "direct_url": null,

    "url": null,

    "metadata": null,

    "architecture": null,

    "hypervisor_type": null,

    "instance_type_rxtx_factor": null,

    "instance_uuid": null,

    "needs_config_drive": null,

    "kernel_id": null,

    "os_distro": null,

    "os_version": null,

    "needs_secure_boot": null,

    "os_shutdown_timeout": null,

    "ramdisk_id": null,

    "vm_mode": null,

    "hw_cpu_sockets": null,

    "hw_cpu_cores": null,

    "hw_cpu_threads": null,

    "hw_disk_bus": null,

    "hw_cpu_policy": null,

    "hw_cpu_thread_policy": null,

    "hw_rng_model": null,

    "hw_machine_type": null,

    "hw_scsi_model": null,

    "hw_serial_port_count": null,

    "hw_video_model": null,

    "hw_video_ram": null,

    "hw_watchdog_action": null,

    "os_command_line": null,

    "hw_vif_model": null,

    "is_hw_vif_multiqueue_enabled": null,

    "is_hw_boot_menu_enabled": null,

    "vmware_adaptertype": null,

    "vmware_ostype": null,

    "has_auto_disk_config": null,

    "os_type": null,

    "os_admin_user": null,

    "hw_qemu_guest_agent": null,

    "os_require_quiesce": null,

    "schema": "/v2/schemas/image",

    "id": "6d787e40-d659-48a1-ae38-613d3d85dee0",

    "name": "cirros-image",

    "location": {

        "cloud": "envvars",

        "region_name": "",

        "zone": null,

        "project": {

            "id": "1e4e7922c48a49dd9d460b852595faae",

            "name": "admin",

            "domain_id": null,

            "domain_name": "demo"

        }

    },

    "tags": []

}

[root@controller ~]# openstack image list

+--------------------------------------+--------------+--------+

| ID                                   | Name         | Status |

+--------------------------------------+--------------+--------+

| 380e567b-660c-4e14-b423-c861c2ea3823 | cirros-image | active |

+--------------------------------------+--------------+--------+

## 2 **Python命令行**
**【题目1】Python 运维开发：云主机类型管理的命令行工具开发[2 分]**

使用已建好的 OpenStack Python 运维开发环境，在/root 目录下创建 flavor_manager.py

脚本，完成云主机类型的管理，flavor_manager.py 程序支持命令行参数执行。提示说明：Python 标准库argparse 模块，可以提供命令行参数的解析。要求如下：

1. 程序支持根据命令行参数，创建 1 个多云主机类型。返回 response。位置参数“create”，表示创建；

参数“-n”支持指定 flavor 名称，数据类型为字符串类型；

参数“-m”支持指定内存大小，数据类型为 int，单位 M；

参数“-v”支持指定虚拟 cpu 个数，数据类型为 int；

参数“-d”支持磁盘大小，内存大小类型为 int，单位 G；

参数“-id”支持指定 ID，类型为字符串。

参考运行实例：

python3 flavor_manager.py create -n flavor_small -m 1024 -v 1 -d 10 -id 100000

1. 程序支持查询目前 admin 账号下所有的云主机类型。位置参数“getall”，表示查询所有云主机类型；

查询结果，以 json 格式输出到控制台。参考执行实例如下：

python3 flavor_manager.py getall

1. 支持查询给定具体名称的云主机类型查询。位置参数“get”，表示查询 1 个云主机类型； 参数“-id”支持指定 ID 查询，类型为 string。 控制台以 json 格式输出创建结果。

参考执行实例如下：

python3 flavor_manager.py get -id 100000

1. 支持删除指定的 ID 的用户。

位置参数“delete”，表示删除一个用户；

参数“-id”支持指定 ID 查询，返回 response，控制台输出response。参考执行实例如下：

python3 flavor_manager.py delete -id 100001

1. 执行 flavor_manager.py 文件创建 1 个云主机类型正确计 0.5 分

1. 执行 flavor_manager.py 文件查询所有云主机类型正确计 0.5 分

1. 执行 flavor_manager.py 文件查询给定具体名称的云主机类型正确计 0.5 分

1. 执行 flavor_manager.py 文件删除指定的 ID 的用户正确计 0.5 分

1.获取openstack token令牌

[root@controller ~]# source /etc/keystone/admin-openrc.sh

[root@controller ~]# openstack token issue

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

| Field      | Value                                                                                                                                                                                   |

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

| expires    | 2022-11-22T08:07:42+0000                                                                                                                                                                |

| id         | gAAAAABjfHU-k2du5UUc5yXk-V8mnFONCVSBJQIbU22vKvvxk7ibL6tsk6GlbCF1VLwWNpwJjMLIRRz_vzZ0v84B71Hk6NvWZnr8Lt1FTsjVLtVjaaZ4KXipMqgMjo3_25LeQgzHKmS87z6eDrTQfsmAwb0Ptl4dFAgHNbsEKsc3c0Od3v0DL2Y |

| project_id | f8f814e0f99a470780e22064242b6881                                                                                                                                                        |

| user_id    | 7930e0d6690c4ec1b42f97202e6d7313                                                                                                                                                        |

+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

2.编写flavor_manager.py

[root@controller ~]# cat flavor_manager.py

import requests,json,argparse

shuju = argparse.ArgumentParser()

shuju.add_argument("option")

shuju.add_argument("-n","--name",type=str)

shuju.add_argument("-m","--ram",type=int)

shuju.add_argument("-v","--vcpus",type=int)

shuju.add_argument("-d","--disk",type=str)

shuju.add_argument("-id","--id",type=int)

headers = {'X-Auth-Token':'gAAAAABkBDudkjAe0YkjbVSP_RVVXktkb0M5tqu41JHPPViMJClR_XmNZuGy7MTl-WSfz1un1vbAm9X8DIhUhBPvQ1OyvmQ-xPo10wIWm5ZXLIRe8AjiAD-MhykszP3Lzbkb5VasgEuSXPfLGIQx_b8MowyPl6basfXcP2ETNm9FIXX6L4lAiNs'}

url = 'http://controller:8774/v2.1/flavors'

def create_flavor(name,id,ram,disk,vcpus):

  data = {'flavor':{'name':name,'disk':disk,'ram':ram,'id':id,'vcpus':vcpus}}

  rsp = requests.post(url,headers=headers,data=json.dumps(data)).json()

  return rsp

def getall():

  rsp = requests.get(url,headers=headers).json()

  return rsp

def get(id):

  url = 'http://controller:8774/v2.1/flavors/'+str(id)

  rsp = requests.get(url,headers=headers).json()

  print(rsp)

def delete(id):

  url = 'http://controller:8774/v2.1/flavors/'+str(id)

  rsp = requests.delete(url,headers=headers)

  return rsp

args = shuju.parse_args()

if args.option=='create':

  print(create_flavor(args.name,args.id,args.ram,args.disk,args.vcpus))

if args.option=='getall':

  print(getall())

if args.option=='get':

  print(get(args.id))

if args.option=="delete":

  print(delete(args.id))

3.运行测试：

（1）程序支持根据命令行参数，创建1个多云主机类型。返回response。

位置参数“create”，表示创建；

[root@controller ~]# python3 flavor_manager.py create -n flavor_small -m 1024 -v 2 -d 10 -id 100002

{'flavor': {'links': [{'href': 'http://controller:8774/v2.1/flavors/100002', 'rel': 'self'}, {'href': 'http://controller:8774/flavors/100002', 'rel': 'bookmark'}], 'ram': 1024, 'OS-FLV-DISABLED:disabled': False, 'os-flavor-access:is_public': True, 'rxtx_factor': 1.0, 'disk': 10, 'id': '100002', 'name': 'flavor_small', 'vcpus': 2, 'swap': '', 'OS-FLV-EXT-DATA:ephemeral': 0}}

（2）程序支持查询目前admin账号下所有的云主机类型。

位置参数“getall”，表示查询所有云主机类型；

查询结果，以json格式输出到控制台。

[root@controller ~]# python3 flavor_manager.py getall

{'flavors': [{'id': '1', 'links': [{'href': 'http://controller:8774/v2.1/flavors/1', 'rel': 'self'}, {'href': 'http://controller:8774/flavors/1', 'rel': 'bookmark'}], 'name': 'm1.tiny'}, {'id': '100002', 'links': [{'href': 'http://controller:8774/v2.1/flavors/100002', 'rel': 'self'}, {'href': 'http://controller:8774/flavors/100002', 'rel': 'bookmark'}], 'name': 'flavor_small'}, {'id': '2', 'links': [{'href': 'http://controller:8774/v2.1/flavors/2', 'rel': 'self'}, {'href': 'http://controller:8774/flavors/2', 'rel': 'bookmark'}], 'name': 'm1.small'}, {'id': '3', 'links': [{'href': 'http://controller:8774/v2.1/flavors/3', 'rel': 'self'}, {'href': 'http://controller:8774/flavors/3', 'rel': 'bookmark'}], 'name': 'm1.medium'}]}

（3）支持查询给定具体名称的云主机类型查询。

位置参数“get”，表示查询1个云主机类型；

参数“-id”支持指定ID查询，类型为string。

控制台以json格式输出创建结果。

[root@controller ~]# python3 flavor_manager.py get -id 100002

{'flavor': {'links': [{'href': 'http://controller:8774/v2.1/flavors/100002', 'rel': 'self'}, {'href': 'http://controller:8774/flavors/100002', 'rel': 'bookmark'}], 'ram': 1024, 'OS-FLV-DISABLED:disabled': False, 'os-flavor-access:is_public': True, 'rxtx_factor': 1.0, 'disk': 10, 'id': '100002', 'name': 'flavor_small', 'vcpus': 2, 'swap': '', 'OS-FLV-EXT-DATA:ephemeral': 0}}

None

（4）支持删除指定ID的云主机类型。

位置参数“delete”，表示删除一个云主机类型；

参数“-id”支持指定ID查询，返回response，控制台输出response。

[root@controller ~]# python3 flavor_manager.py delete -id 100002

<Response [202]>

**【题目2】Python 运维开发：用户管理的命令行工具开发[2 分]**

使用已建好的OpenStack Python 运维开发环境，在/root 目录下创建 user_manager.py 脚本，完成用户管理功能开发，user_manager.py 程序支持命令行带参数执行。

提示说明：Python 标准库argparse 模块，可以提供命令行参数的解析。

1. 程序支持根据命令行参数，创建 1 个用户。

位置参数“create”，表示创建；

参数“-i 或--input”，格式为 json 格式文本用户数据。查询结果，以 json 格式输出到控制台。

参考执行实例如下：

python3 user_manager.py create --input '{ "name": "user01", "password": "000000", "description": "description" } '

1. 支持查询给定具体名称的用户查询。

位置参数“get”，表示查询 1 个用户；

参数“-n 或 --name”支持指定名称查询，类型为 string。

参数“-o 或 output”支持查询该用户信息输出到文件，格式为json 格式。参考执行实例如下：

python3 user_manager.py get --name user01-o user.json

1. 程序支持查询目前 admin 账号下所有的用户。位置参数“getall”，表示查询所有用户；

参数“-o 或--output”支持输出到文件，格式为 yaml 格式。参考执行实例如下：

python3 user_manager.py getall -o openstack_all_user.yaml

1. 执行user_manager.py 文件正确创建用户计 0.5 分

1. 执行user_manager.py 文件正确查询用户计 0.5 分

1. 执行user_manager.py 文件正确查询所有用户计 0.5 分

1. 执行user_manager.py 文件正确删除用户计 0.5 分

1. 支持删除指定的名称的用户。

位置参数“delete”，表示删除一个用户；返回 response，通过控制台输出。参数“-n 或--name”支持指定名称查询，类型为 string。

参考执行实例如下：

python3 user_manager.py delete -name user01

1.获取openstack token令牌和demo id

[root@controller ~]# openstack domain list

+----------------------------------+---------+---------+--------------------------+

| ID                               | Name    | Enabled | Description              |

+----------------------------------+---------+---------+--------------------------+

| 720f960530dc4e1982ebc7bdfd261f86 | demo    | True    | Default Domain           |

| default                          | Default | True    | The default domain       |

| e62a73d6033d49e89a0fd711a87f7d7b | heat    | True    | Stack projects and users |

+----------------------------------+---------+---------+--------------------------+

2.编写user_manager.py

```shell
import requests,json,argparse
shuju = argparse.ArgumentParser()
shuju.add_argument("option")
shuju.add_argument('-n','--name',type=str)
shuju.add_argument('-o','--output')
shuju.add_argument('-i','--input',type=str)
headers = {'X-Auth-Token':'gAAAAABkBV4BaV4VqNcEN9LQ5R2uBO_zogTXMjOLqkBZPMG3g3OXwmfdiHY7IHNXgQc_RSsbRkjDV4eOIeqPtuDtbr_ovK4Db1X3BsQU0jvcvPjFMsSKb_35QVJ8eK94RKAgkRFK-NIUJmex7xGmxyKulqXu59I_ZtrV444YGzhxPlQz9AU_JSE'}
url = 'http://controller:5000/v3/users'
#domain_id是上面demo的id,填入下面
def create_user(ss):
    data = json.loads(ss)
    data['domain_id'] = '720f960530dc4e1982ebc7bdfd261f86'
    data = {'user':data}
    rsp = requests.post(url,headers=headers,data=json.dumps(data)).json()
    return rsp
def get(id):
    url = 'http://controller:5000/v3/users?name='+str(id)
    rsp = requests.get(url,headers=headers).json()
    with open('user.json','w') as file:
        file.write(str(rsp))
    return rsp
def getall():
    rsp = requests.get(url, headers=headers).json()
    with open('openstack_all_user.yaml', 'w') as file:
        file.write(str(rsp))
    return rsp
def delete(id):
    s = json.dumps(get(id))
    ss = json.loads(s)['users'][0]['id']
    url = 'http://controller:5000/v3/users/'+ss
    print(url)
    rsp = requests.delete(url,headers=headers)
    return rsp
args = shuju.parse_args()
if args.option=="create":
    print(create_user(args.input))
if args.option=="get":
    print(get(args.name))
if args.option=='getall':
    print(getall())
if args.option=='delete':
print(delete(args.name))
```

3.运行测试：

（1）程序支持根据命令行参数，创建1个用户。

位置参数“create”，表示创建；

参数“-i 或--input”，格式为json格式文本用户数据。

查询结果，以json格式输出到控制台。

[root@controller ~]# python3 user_manager.py create --input '{ "name": "usertest", "password": "passw0rd", "description": "desc" } '

{'error': {'code': 409, 'message': 'Conflict occurred attempting to store user - Duplicate entry found with name usertest at domain ID 720f960530dc4e1982ebc7bdfd261f86.', 'title': 'Conflict'}}

（2）支持查询给定具体名称的用户查询。

位置参数“get”，表示查询1个用户；

参数“-n 或 --name”支持指定名称查询，类型为string。

参数“-o 或 output”支持查询该用户信息输出到文件，格式为json格式。

[root@controller ~]# python3 user_manager.py get --name usertest -o user.json && cat user.json

{'users': [{'description': 'desc', 'name': 'usertest', 'domain_id': '720f960530dc4e1982ebc7bdfd261f86', 'enabled': True, 'links': {'self': 'http://controller:5000/v3/users/570661990adc4685a2c13f5049a782d2'}, 'options': {}, 'id': '570661990adc4685a2c13f5049a782d2', 'password_expires_at': None}], 'links': {'self': 'http://controller:5000/v3/users?name=usertest', 'previous': None, 'next': None}}

{'users': [{'description': 'desc', 'name': 'usertest', 'domain_id': '720f960530dc4e1982ebc7bdfd261f86', 'enabled': True, 'links': {'self': 'http://controller:5000/v3/users/570661990adc4685a2c13f5049a782d2'}, 'options': {}, 'id': '570661990adc4685a2c13f5049a782d2', 'password_expires_at': None}], 'links': {'self': 'http://controller:5000/v3/users?name=usertest', 'previous': None, 'next': None}}

（3）程序支持查询目前admin账号下所有的用户。

位置参数“getall”，表示查询所有用户；

参数“-o 或--output”支持输出到文件，格式为yaml格式。

[root@controller ~]# python3 user_manager.py getall -o openstack_all_user.yaml && cat openstack_all_user.yaml

…… 输出内容太多，省略一下

（4）支持删除指定的名称的用户。

位置参数“delete”，表示删除一个用户；返回response，通过控制台输出。

参数“-n或--name”支持指定名称查询，类型为string。

[root@controller ~]# python3 user_manager.py delete --name usertest

http://controller:5000/v3/users/570661990adc4685a2c13f5049a782d2

<Response [204]>
