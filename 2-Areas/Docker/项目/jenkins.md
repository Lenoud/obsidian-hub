# Jenkins CI/CD 持续集成

开源的自动化服务器，用于持续集成和持续交付（CI/CD），支持构建、部署和自动化测试。

## 部署

```yaml
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
      - /usr/local/bin/docker:/usr/bin/docker
      - /etc/localtime:/etc/localtime:ro
    labels:
      createdBy: Apps
networks: {}
```

## Pipeline 示例

```groovy
pipeline {
 agent{
	label "docker"
	}
    stages {
        stage('Run Docker Images') {
            steps {
                script {
                    sh 'docker images'
                }
            }
        }

        stage('Run Docker ps') {
            steps {
                script {
                    sh 'docker ps'
                }
            }
        }
    }
}
```

## 说明

- 访问地址：`http://<IP>:8080`
- 端口映射：`8080` -> `8080`
- `privileged: true`：赋予容器特权模式
- 挂载 Docker socket 和 CLI：支持在 Jenkins 中执行 Docker 命令
  - `/var/run/docker.sock` -> Docker daemon socket
  - `/usr/local/bin/docker` -> Docker CLI
- 数据持久化：`./data` 映射到 `/var/jenkins_home`
