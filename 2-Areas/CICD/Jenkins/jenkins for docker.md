# jenkins for docker

jenkins for docker相关技术笔记。

## docker部署
```bash
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins:2.462.3-lts
    restart: always
    privileged: true
    user: root
    ports:
      - 8080:8080
    volumes:
      - ./data:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker
        #docker 二进制文件存放目录
    labels:
      createdBy: "Apps"
```

## 首次查看密码
```shell
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

## 相关笔记

- [[jenkins]]
- [[MOC-Docker]]
