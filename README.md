# Development Box

本地开发环境配置

## Pre-requisite

- docker
- docker-compose

### zsh plugin for docker (optional)

with oh-my-zsh, in `.zshrc`

`plugins=(... docker docker-compose)`

## Middleware环境

- Ubuntu: `docker-compose -f docker-compose-ubuntu.yml --env-file .env.ubuntu up -d`
- Windows: `docker-compose up -d`

## Continuous Integration环境

- Ubuntu: `docker-compose --env-file .ubuntu.env up -d`
- Windows: `docker-compose up -d`

## 开发环境

### JHipster

`docker pull elasticsearch`

`mkdir ~/mars-rover`

`docker container run --name jhipster -v /home/atom/SharedFolder/mars-rover:/home/jhipster/app -v ~/.m2:/home/jhipster/.m2 -p 8085:8080 -p 9003:9000 -p 3001:3001 -d -t jhipster/jhipster`

Enter container as user root, `npm install` may needs root access:
`docker exec -it -u root jhipster bash`

Create application
`jhipster`

### 监控环境

## 环境配置

### Jenkins

localhost:8082

- user:bitnami

### Sonarqube

localhost:9000

- admin:test

## 常用操作

### Image管理

### Image更新

#### Get the updated images

`$ docker pull bitnami/jenkins:latest`

#### Stop your container

- For docker-compose: `$ docker-compose stop jenkins`
- For manual execution:`$ docker stop jenkins`

#### 应用数据备份/状态保存

`$ rsync -a /path/to/jenkins-persistence /path/to/jenkins-persistence.bkp.$(date +%Y%m%d-%H.%M.%S)`

You can use this snapshot to restore the application state should the upgrade fail.

#### Remove the stopped container

- For docker-compose: `$ docker-compose rm -v jenkins`
- For manual execution: `$ docker rm -v jenkins`

#### Run the new image

- For docker-compose:`$ docker-compose up jenkins`
- For manual execution (mount the directories if needed): `docker run --name jenkins bitnami/jenkins:latest`

### Container管理

执行容器命令
`docker exec -it <docker_container_name>  /bin/bash`

以root用户执行
`docker exec -it <docker_container_name> -u root /bin/bash`

查看文件内容：
`docker exec my-jenkins-1 ls -l /var/jenkins_home`

删除所有容器：

- with bash：`docker rm ${docker ps -a -q}`
- with zsh：`docker ps -a -q | xargs docker rm`

### Volume管理

named volume在windows 10环境里有问题，在docker-compose.yml文件里每个service单独设置bind volume。

`docker run -v "$(pwd)":[volume_name] [docker_image]`

创建named volume
`docker volume create --name vol-test --opt type=none --opt device=/c/Users/mau2sgh/atom/workspace/Playground/play-container/vol-test`

备份volume数据：
`docker cp <container id>:/path/in/container /path/in/host`

删除所有volume：

- `docker volume prune`
- `docker volume ls | awk {'print $2'} | xargs docker volume rm`

> 注意bind mount和named volume的区别：
>
> - bind mount即在运行时指定mount路径，若不存在默认会自动创建
> - named volume由docker引擎管理，可使用`docker inspect`查看

### Network管理

#### Proxy设置

构建容器时
`docker build --build-arg http_proxy=<http://10.173.232.36:3128> --build-arg https_proxy=<http://10.173.232.36:3128> . -t acr-tutorial-app`

创建容器时
`docker run --env http_proxy= --env https_proxy=<http://10.173.232.36:3128> -p 1880:1880 -v node_red_user_data:/data --name mynodered nodered/node-red`

### 状态检查

检查容器状态：
`docker-compose ps`
