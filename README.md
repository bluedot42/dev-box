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

#### 执行容器命令

`docker exec -it <docker_container_name>  /bin/bash`

#### 以root用户执行

`docker exec -it <docker_container_name> -u root /bin/bash`

#### 查看文件内容

`docker exec my-jenkins-1 ls -l /var/jenkins_home`

#### 删除所有容器

- with bash：`docker rm ${docker ps -a -q}`
- with zsh：`docker ps -a -q | xargs docker rm`

#### 停止容器

`docker-compose -f src/main/docker/app.yml stop`
`docker container stop <container_id>`

#### 检查容器状态

`docker-compose ps`

`docker container stats $(docker container ps --format={{.Names}})`

#### Scale a container

`docker-compose scale test-app=4` to have 4 instances of application “test” running.

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

## Proxy

### Px

#### 安装Px

- [Px](https://github.com/genotrance/px/releases)

#### 配置文件

Place the attached px.ini in the same folder: px.ini

```ini

[proxy]
gateway = 0
hostonly = 1
listen = 127.0.0.1
noproxy = 127.0.0.*,10.*.*.*,192.168.*.*
port = 3128
server = rb-proxy-de.bosch.com:8080

[settings]

; Run in foreground when frozen or with pythonw.exe. 0 or 1, default: 0
;   Px will attach to the console and write to it even though the prompt is
;   available for further commands. CTRL-C in the console will exit Px
foreground = 0

; Idle timeout in seconds for HTTP connect sessions before closing the connection
idle = 30

; Enable logging
;   Logs are written to the same directory that Px is run and are over-written on startup
log = 0

; Timeout in seconds for connections before giving up
socktimeout = 5

; Number of parallel threads per worker (process)
threads = 8

; Number of parallel workers (processes)
workers = 3
```

#### 激活服务

Open the Px folder via commandline and execute: px.exe --install
A restart is required. Px should start automatically on Windows startup, the Task Manager should show a list of px.exe running.

#### 配置proxy

```powershell
setx -m HTTP_PROXY "http://127.0.0.1:3128"
setx -m HTTPS_PROXY "http://127.0.0.1:3128"
setx -m no_proxy "localhost,.bosch.com,.bosch-iot-cloud.com"
```

### Cntlm

127.0.0.1:3128

### NPM configuration

```bash
npm config set proxy <http://username:password@host:port>
npm config set https-proxy <http://username:password@host:port>
```

或直接修改`~/.npmrc`:

```ini
proxy=<http://username:password@host:port>
https-proxy=<http://username:password@host:port>
https_proxy=<http://username:password@host:port>
```

### Yarn configuration

```bash
yarn config set proxy <http://username:password@host:port>
yarn config set https-proxy <http://username:password@host:port>
```

### Git configuration

```bash
git config --global http.proxy <http://username:password@host:port>
git config --global https.proxy <http://username:password@host:port>
```

或直接修改`~/.gitconfig`:

```ini
[http]
        proxy = <http://username:password@host:port>
[https]
        proxy = <http://username:password@host:port>
```

### Maven configuration

直接修改`~/.m2/settings.xml`:

```xml
<proxies>
    <proxy>
        <id>id</id>
        <active>true</active>
        <protocol>http</protocol>
        <username>username</username>
        <password>password</password>
        <host>host</host>
        <port>port</port>
        <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
</proxies>
```

### Maven Wrapper

Create a new file .mvn/jvm.config inside the project folder and set the properties accordingly:

```ini
-Dhttp.proxyHost=host
-Dhttp.proxyPort=port
-Dhttps.proxyHost=host
-Dhttps.proxyPort=port
-Dhttp.proxyUser=username
-Dhttp.proxyPassword=password
```

### Gradle configuration

Add the below in your gradle.properties file and in your `gradle/wrapper/gradle-wrapper.properties` file if you are downloading the wrapper over a proxy

If you want to set these properties globally then add it in `USER_HOME/.gradle/gradle.properties` file

```ini
## Proxy setup
systemProp.proxySet="true"
systemProp.http.keepAlive="true"
systemProp.http.proxyHost=host
systemProp.http.proxyPort=port
systemProp.http.proxyUser=username
systemProp.http.proxyPassword=password
systemProp.http.nonProxyHosts=local.net|some.host.com

systemProp.https.keepAlive="true"
systemProp.https.proxyHost=host
systemProp.https.proxyPort=port
systemProp.https.proxyUser=username
systemProp.https.proxyPassword=password
systemProp.https.nonProxyHosts=local.net|some.host.com
## end of proxy setup
```

### Docker

Depending on your OS, you have to edit a specific file (/etc/sysconfig/docker or /etc/default/docker).

Then, you have to restart the docker service with: `sudo service docker restart`.

#### 构建容器

`docker build --build-arg http_proxy=<http://10.173.232.36:3128> --build-arg https_proxy=<http://10.173.232.36:3128> . -t acr-tutorial-app`

#### 创建容器

`docker run --env http_proxy= --env https_proxy=<http://10.173.232.36:3128> -p 1880:1880 -v node_red_user_data:/data --name mynodered nodered/node-red`

#### Windows Docker desktop

host.docker.internal
