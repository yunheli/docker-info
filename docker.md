# Docker
- 隔离应用(依赖)
- 维护镜像
- 创建易于分发的应用
- ... ... 

## 从hello-world开始

#### centos && ubuntu 安装
``` bash
# yum update  or apt-get update 
# curl -fsSL https://get.docker.com/ | sh
# service docker start
``` 

#### osx 直接下载app安装

```
https://download.docker.com/mac/beta/Docker.dmg
```

#### hello-world
``` bash
# docker pull library/hello-world
# docker run hello-world
```

## docker 核心组件

- docker deamon: `守护进程,运行于宿主机上`
- docker client: `cli 工具，用户接口`
- docker image & container: `创建container的模板，类似于OOP中的类和对象`

#### docker client
```
Usage: 
	docker [OPTIONS] COMMAND [arg...]
	docker COMMAND --help
```

##### pull
*从registry拉取image*

```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]

# docker pull centos:6.7
# docker pull index.tenxcloud.com/tenxcloud/mysql:latest
```

##### run: 
*从image创建容器* 
*程序执行完成后，容器即终止*

```
docker run IMAGE[:tag] [COMMAND] [ARG...]

# docker run centos:6.7 ifconfig
# docker run --rm -it centos:6.7 bash
# docker run --rm -it -d app 

```
常用选项:

```
-t, --tty #连接到tty接口
-i, --interactive # 打开终端,在交互式模式下运行
-d 后台运行容器并且打印出此容器的ID
--name 设定容器名称,不指定则会自动生成
--rm 容器关闭后自动删除容器,同时删除其卷
-h, --hostname 指定HOSTNAME
-m 限制使用的内存
--cpuset-cpus #设定运行的cpu
--shm-size  使用的最大共享内存大小
```


#### ps       
*列出正在运行的container*

```
docker ps [OPTIONS]

# docker ps 
# docker ps -a
# docker ps -s
```

```
-a选项可以列出所有容器,包括已经关掉的
-s可以显示出资源占用
```

##### exec
*在运行的容器中执行命令*

```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

# docker exec -it container-id bash
```

##### images
*列出镜像列表*

```
docker images [OPTIONS] [REPOSITORY[:TAG]]

# docker images
# docker images -aq
```

##### inspect
*查看镜像或容器的底层详细信息和内部信息,json格式,可以使用-f, --format指定显示模版,如:{{.key1.key2....}}*

```
docker inspect [OPTIONS] CONTAINER|IMAGE [CONTAINER|IMAGE...]

# docker inspect centos:6.7
# docker inspect container-id
# docker inspect -f '{{.Created}}' container-id

```

##### top
*查看中运行容器的进程状态*

```
docker top CONTAINER [ps OPTIONS]

# docker top container-id 
```


##### logs
*查看容器日志,容器内部的操作记录*

```
docker logs [OPTIONS] CONTAINER-ID

# docker logs container-id
# docker logs -f container-id

```

##### rm && rmi
*删除本地容器或者镜像*

```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
docker rmi [OPTIONS] IMAGE [IMAGE...]

# docker rm container-id
# docker rmi hello_workd
```
其他选项: 

```
-f 强制删除
```

##### tag
*给镜像打标签*

```
docker tag IMAGE[:TAG] IMAGE[:TAG]

# docker tag centos:6.7 centos67:latest
```

##### login && logout
*登录/登出 registry*

```
docker login|logout [SERVER]

# docker login|logout registry.cn-hangzhou.aliyuncs.com
```


##### push
*推送本地image到registry*

```
docker push [OPTIONS] NAME[:TAG]

# docker push registry.cn-hangzhou.aliyuncs.com/firhq/mysql:latest
```

##### kill
*杀死正在运行的容器*

```
docker kill CONTAINER [CONTAINER...]

# docker kill container-id
```

##### info 
*查看docker daemon相关信息*

```
# docker info
```

#### data Volume
*数据卷是供一个或多个容器使用的文件或目录*

- 可以共享于多个容器之间；
- 对数据卷的修改会立即生效；
- 对数据卷的更新与镜像无关；
- 数据卷会一直存在；除非手动删除

##### 使用方式：
- `-v /MOUNT_POINT` : **默认映射的宿主机路径：/var/lib/docker/volumes/,每次都会重新创建**
- `-v /HOST/DIR:/CONTAINER/DIR` : **映射主机指定目录否则文件**
- **在Dockerfile中使用VOLUME指令定义**

 
```
# docker run --rm -v $(pwd):/data centos:6.7 bash -c "ifconfig &> /data/if.info"
```

