# drone.yml流水线文件

drone.yml流水线文件相关技术笔记。

```yaml
kind: pipeline
type: docker
name: default

steps:
- name: backend
  image: golang
  commands:
  - go version
  - ls  -hla /drone/src/
  - sleep
```

## 需要挂载volume需要勾选Trusted
![[1702873299704-fbd02ce2-1aff-4290-b6a3-219f29e25547.png]]

```yaml
#docker测试流水线
kind: pipeline
type: docker
name: docker-ps

steps:
- name: docker-ps
  image: docker:20.10.9-dind
  commands:
    - docker ps
  volumes:
    - name: dockersock
      path: /var/run/docker.sock

volumes:
  - name: dockersock
    host:
      path: /var/run/docker.sock
```

## 指定runner运行流水线
```yaml
node:
  machine1: runner1
#- DRONE_RUNNER_LABELS=machine1:runner1
```

## 禁用默认的clone
```yaml
clone:
  disable: true
```

## 手动clone的思路
1. 新建一个用于存放git仓库的目录/srv/git
2. clone你的项目/srv/git/projectA
3. map 服务器的.ssh目录用于鉴权，当然也可以用drone的secret
4. 在drone yml中禁用clone
5. 增加一个pull step用于同步更新，取代系统默认的clone，并拷贝到drone工作目录

### .drone.yml示例
```yaml
kind: pipeline
type: docker
name: default

clone:
  disable: true

steps:
  - name: pull
    image: alpine/git
    volumes:
      - name: ssh
        path: "/root/.ssh"
      - name: git_repo
        path: "/srv/git_repo"
    commands:
      - cd /srv/git_repo/projectA
      - git pull origin main
      - cd -
      - "cp -R /srv/git_repo/carry-dockers/* ./"
volumes:
  - name: ssh
    host:
      path: /root/.ssh
  - name: git_repo
    host:
      path: /srv/git_repo
```
