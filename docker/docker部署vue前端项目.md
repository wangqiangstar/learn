## docker基础

### 什么是daocker

> docker 是一个虚拟环境容器，可以将开发环境、代码、配置文件等一并打包到这个容器中，最后发布应用

### 使用docker

> 通过将部署的操作集中成一个部署脚本完成传统的部署流程，通过在服务器上运行docker容器来运行前端应用

### 部署环境

1. vue cli --version 3.3.0
2. CentOS Linux release 7.7
3. docker-ce 社区版

### 如何安装

```
yum install docker-ce
```

部署项目需要准备Dockerfile和nginx.conf

### Dockerfile配置

>dockerfile是一个配置文件，用来让docekr build命令清除运行哪些操作，创建dockerfile并编写相关配置

```
FROM node:latest as builder
WORKDIR /app
COPY package.json
RUN npm install
COPY . .
RUN npm run build



FROM nginx:latest
COPY nginx.conf /etc/nginx
COPY --from=builder /app/dist /usr/share/nginx/html

//ps: 每一个指令的前缀都必须是大写的
```

- ADD和COPY：将文件或目录赋值到Dockerfile构建的镜像中
- EXPOSE：指定运行该镜像的容器使用的端口，可以是多少
- RUN：指令告诉docker在镜像内执行命令
- FROM：通过FROM指定的镜像名称，这个镜像称之为基础镜像，必须位于第一条非注释指令
- WORKDIR：在容器内部设置工作目录

### Nginx.conf配置如下

```
events {
	worker_connections 1024;
}
http{
	server {
		listen 80;
		server_name localhost;
		location / {
			root /usr/share/nginx/html;
			index index.html index.htm;
		}
		error_page 500 502 503 504 /50x.html;
		location = /50x.html {
			root /usr/share/nginx/html;
		}
	}
}
```

创建文件并编写后，用docker创建镜像

### 创建镜像

使用当前目录的Dockerfile 创建镜像，标签为 frontend

```
docker build -t frontend .
```

- -t : 指定要创建的目标镜像
- . : Dockerfile 文件所在目录，可以指定Dockerfile的绝对路径

运行之后镜像成功生成

### 本地镜像

```
docker image ls | grep frontend
```

出现结果则应用镜像 frontend 成功创建，然后我们基于该镜像启动一个Docker 容器



### 用容器启动镜像

使用docker镜像frontend:latest以指定80端口映射模式启动容器，并将容器命名为frontend

```
docker run --name frontend -p 80:80 frontend:latest
```

- -p : 指定端口映射，格式为： 主机（宿主）端口:容器端口 将宿主的80端口映射到容器的80端口
- --name : 为容器指定一个名称

### 完成docker部署

访问80端口，成功进入页面

### 其他常用docker命令

```
docker rm -f [DOCKER...] // 删除docker

dcoker ps -a // 查看所有容器

docker rm [container_id] // 删除一个容器

docker stop [container_id] // 停止一个容器

docker start [container_id] //启动一个容器

docker describe [container_id]

docker images // 查看所有镜像

dcoker rmi [IMAGE...] // 删除镜像
```

> 这里有个`docker start container_id`， 启动一个容器，说明容器即使退出后其资源依然存在，还可以使用`docker start`重启这个容器。要想让容器退出后自动删除可以在`docker run`时指定`--rm`参数。









## docker实战

### 镜像与容器

docker 中有两个重要概念。

一个是容器（container）：容器特别像一个虚拟机，容器中运行着一个完整得操作系统。可以在容器中装nodejs，可以执行 `npm install` ，可以做一切你当前操作系统能做得事情。

另一个是镜像（image）：镜像是一个文件，它是用来创建容器的。如果你有装过 windows 操作系统，那么 docker 镜像特别像 “win7纯净版.rar”文件

上面就是docker的全部基础知识，就这么简单

顺便一提，在 docker 中，我们通常称你当前使用的真是操作系统为“宿主机”（Host）

### 安装docekr

安装 docker 在你的电脑上就像安装 vs code 一样简单

> 如果你是的是 windows电脑，需要购买支持虚拟化的版本。如win10专业版、win10家庭版是不行的

- [Mac](https://download.docker.com/mac/stable/Docker.dmg)
- [Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe)
- [Linux](https://get.docker.com/)

安装完 docker 后，你可能会发现自己可以打开一个漂亮的 docekr 窗口，其实这个窗口没什么用处，通常我们都是通过CLI命令行的方式操作 docker 的，就像 Git 一样

### 运行docker

接下里我们搭建一个能够托管静态资源文件的nginx服务器

容器运行程序，而容器哪来的呢？容器是镜像创建出来的。那镜像又是哪来的呢？

镜像是通过一个Dockerfile 打包来的，它非常像我们前端的 `package.json`文件

所以创建关系为“

```
Dockerfile: 类似于“package.json”
 |
 V
Image: 类似于“Win7纯净版.rar”
 |
 V
Container: 一个完整操作系统

```

### 创建文件

我们创建一个目录`hello-docker`，在目录中创建一个 index.html文件，内容为：

```
<h1>hello docker</h1>
```





















