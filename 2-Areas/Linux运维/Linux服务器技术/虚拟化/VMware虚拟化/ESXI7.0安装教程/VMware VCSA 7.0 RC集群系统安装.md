# VMware VCSA 7.0 RC集群系统安装

VMware VCSA 7.0 集群管理系统安装。

## 安装集群管理节点
许可证：406DK-FWHEH-075K8-XACO6-0JHO8

管理界面：5480端口

解压iso安装文件后在vcsa-ui-installer中找到win32-installer.exe开始安装

默认登录账号  administrator@vsphere.local  密码需要大小写数字加特殊符号

选择你要创建vCenter所在的主机

![[1703570156388-83b9f9c9-09ff-4ce4-adb0-1ff031378594.png]]

在这里插入图片描述

5、提示证书警告，选择“是”。

![[1703570156299-f33398be-295f-4a94-8508-43cd2a06537a.png]]

在这里插入图片描述

6、填写vCenter的机器名字以及密码，这里的密码是登录vc控制台root的密码

![[1703570156300-36d32290-483b-479d-898d-c7a68b1fd1e2.png]]

在这里插入图片描述

7、选择小环境配置

![[1703570156398-d1a30a4c-95b4-4fed-96a8-3220e579d277.png]]

在这里插入图片描述

8、机器存放的位置以及选择精简模式

![[1703570156438-ede305e1-5914-4a2a-a5a8-5e4dafcc2dc1.png]]

在这里插入图片描述

9、配置网络虚拟机信息

![[1703570156912-7067d53b-cde8-4e30-8aaa-faf783eb23d8.png]]

在这里插入图片描述

10、确认各项信息，并点击完成

![[1703570156963-10b19f9e-07ca-4869-93cd-478805936f5b.png]]

在这里插入图片描述

11、已经开始安装，需要等待十来分钟

![[1703570157267-88d3a9b9-6409-48bd-827d-252e8cd72d31.png]]

在这里插入图片描述

### 12、第一阶段已经完成
![[1703570157277-a4f0d196-50b9-4387-b243-128d390decb9.png]]

在这里插入图片描述

13、开始第二阶段的安装工作

![[1703570157398-dacbebf6-84d9-4c5a-bfb2-be896f4657ea.png]]

在这里插入图片描述

14、选择与ESXI时间同步，禁用SSH

![[1703570157668-d42db8bf-fd2d-462d-aaa5-8143eaf2fb0e.png]]

在这里插入图片描述

15、配置SSO信息

![[1703570157709-ed318fbb-eb36-4d3a-820a-b299af1ac72a.png]]

在这里插入图片描述

16、确认加入CIEP

![[1703570157970-89b9fb71-6336-4337-b420-578108159760.png]]

在这里插入图片描述

17、确认第二阶段输入信息

![[1703570157973-b5d8e402-49a2-4590-99d9-057b497224ec.png]]

在这里插入图片描述

18、直接点击OK

![[1703570158255-f287dcce-5160-4f9a-ab1d-4b632fffa7a0.png]]

在这里插入图片描述

19、确认配置信息

![[1703570158429-cd76bfca-d8be-4646-9ed9-ebd91b267dfe.png]]

在这里插入图片描述

20、开始配置。等待十几分钟

![[1703570158543-9d009134-03c8-419a-9a25-439e51dd0ba9.png]]

在这里插入图片描述

20、如下是安装完成之后的界面

![[1703570158508-4dd54773-a0ac-49f0-9fd5-c5880197cd7d.png]]

在这里插入图片描述

![[1703570158789-df9da267-75eb-4cf8-80c7-45ceed7ef06c.png]]

在这里插入图片描述

## 参考博客
[https://blog.csdn.net/baidu_39512534/article/details/128031457](https://blog.csdn.net/baidu_39512534/article/details/128031457)

[https://blog.csdn.net/allway2/article/details/105975649](https://blog.csdn.net/allway2/article/details/105975649)

[https://cloud.tencent.com/developer/article/1970819](https://cloud.tencent.com/developer/article/1970819)
