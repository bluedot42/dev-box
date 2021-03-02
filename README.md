# Development Box

本地开发环境配置

## Volumes

## Ubuntu

`docker-compose -f docker-compose-ubuntu.yml --env-file .env.ubuntu up -d`

## Windows

`docker-compose up -d`

## Common commands

`docker exec -it <docker_container_name>  /bin/bash`

named volume在windows 10环境里有问题，在docker-compose.yml文件里每个service单独设置bind volume。

检查容器状态：
`docker-compose ps`

## Volume operation

查看文件内容：
`docker exec my-jenkins-1 ls -l /var/jenkins_home`

Backup files
`docker cp <container id>:/path/in/container /path/in/host`

`docker volume create --name vol-test --opt type=none --opt device=/c/Users/mau2sgh/atom/workspace/Playground/play-container/vol-test`

## Clean up

docker rm ${docker ps -a -q}

docker volume prune

## Proxy

docker build --build-arg http_proxy=<http://10.173.232.36:3128> --build-arg https_proxy=<http://10.173.232.36:3128> . -t acr-tutorial-app

docker run --env http_proxy= --env https_proxy=<http://10.173.232.36:3128> -p 1880:1880 -v node_red_user_data:/data --name mynodered nodered/node-red

docker exec -it <mycontainer> bash

docker run -v "$(pwd)":[volume_name] [docker_image]

## Configurations

### Jenkins

### Sonarqube

<http://localhost:9000/>

- admin:test
