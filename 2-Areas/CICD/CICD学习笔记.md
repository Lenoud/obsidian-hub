# CICD 学习笔记

CI/CD(Continuous Integration / Continuous Delivery / Continuous Deployment)是现代软件工程的核心实践,通过自动化流水线实现代码从提交到发布的端到端流程。

## CI/CD 三层概念

| 概念 | 全称 | 含义 | 触发频率 |
| --- | --- | --- | --- |
| **CI** | Continuous Integration(持续集成) | 开发者代码合并到主干,自动跑构建+测试 | 每次 push |
| **CD** | Continuous Delivery(持续交付) | CI 之后,自动把可发布制品打包到制品库,等待人工点"发布" | 每次构建成功 |
| **CD** | Continuous Deployment(持续部署) | 制品通过测试后,自动部署到生产,**无人工干预** | 每次制品就绪 |

> CI 是基础,CD 有两种含义,根据上下文判断。

## 主流工具对比

| 工具 | 架构 | 特点 | 适合场景 |
| --- | --- | --- | --- |
| **Jenkins** | 主从 | 老牌、插件多、灵活、UI 一般 | 传统企业、复杂流水线 |
| **GitLab CI** | 内置 | GitLab 自带,配置 `.gitlab-ci.yml` | GitLab 用户 |
| **Drone CI** | 容器原生 | 每步一个 Docker 容器,轻量 | 小团队、Docker 化项目 |
| **GitHub Actions** | 云原生 | GitHub 自带,yaml 定义 | 开源项目、GitHub 用户 |
| **CircleCI / Travis CI** | SaaS | 云托管,免费有限 | 开源、轻量 |
| **Argo CD / Flux** | GitOps | Kubernetes 原生,基于 Git | 云原生 K8s 部署 |

## 流水线典型阶段

```text
1. Checkout      拉 git 代码
2. Lint          静态检查(ESLint / golangci-lint)
3. Build         编译、打包(Maven / npm / go build)
4. Test          单元测试 + 集成测试
5. Scan          安全扫描(SAST、依赖漏洞)
6. Image         构建 Docker 镜像,推送 Registry
7. Deploy Dev    部署到开发环境
8. Deploy Prod   部署到生产(可手动 gate)
```

## GitLab Runner 下载

> 官方包仓库:<https://packages.gitlab.com/runner/>

```bash
# CentOS / RHEL
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.rpm.sh | sudo bash
yum install -y gitlab-runner

# Ubuntu / Debian
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
apt install -y gitlab-runner

# 注册到 GitLab
gitlab-runner register
# 输入 GitLab URL、token、executor(docker/shell/kubernetes)
```

## .gitlab-ci.yml 最小示例

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  image: golang:1.21-alpine
  script:
    - go build -o myapp .
  artifacts:
    paths:
      - myapp

test:
  stage: test
  image: golang:1.21-alpine
  script:
    - go test ./...

deploy:
  stage: deploy
  script:
    - ssh prod-server "cd /opt/myapp && git pull && systemctl restart myapp"
  only:
    - main
```

## 相关笔记
- [[MOC-CICD]] — 各 CI/CD 工具的实战笔记
- [[Gitlab-CI.tar.gz]]
- [[gitlab-ci-demo2048(国赛名字)]]
- [[Drone / drone+gogs轻量级cicd]]
- [[Jenkins / jenkins for docker]]
