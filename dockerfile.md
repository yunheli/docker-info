# dockerfile

**文本文件，可以认为镜像文件构建脚本；由一系列用于根据基础镜像构建新的镜像文件的专用指令序列组成；完成后可使用docker build构建镜像**

---

### Dockerfile的构成:

- 指令行：由指令及指令参数构成；
- 指令：其字符不区分大小写；约定俗成，要使用全大写字符；完成基础镜像选定、安装必要的程序、复制配置文件和数据文件、自动运行的服务以及要暴露的端口等；
- 注释行：#开头的行，必须单独位于一行当中；`#`必须顶格
- 空白行：会被忽略；

### FROM指令
**必须是第一个非注释行(第一个指令)，用于指定所用到的基础镜像；如果本地无基础镜像,则会到registry下载.**

语法格式：

```
FROM  <image>[:<tag>] 或
FROM  <image>@<digest> #<digest>为镜像的校验码,

例如:
FROM busybox:latest
FROM centos:6.7

注意：不要在一个dockerfile文件中使用多个FROM指令；
```

### MAINTANIER指令
**用于提供信息的指令，用于让作者提供本人的信息；不限制其出现的位置，但建议紧跟在FROM之后**

语法格式：

```
MAINTANIER  <author's detail>

例如：
MAINTANIER  Mayi<my3157@hotmail.com>
```

### COPY指令
**用于从docker主机复制文件至正在创建的映像文件中；**

语法格式：

```
COPY  <src> ...  <dest>
COPY  ["<src>","<src>",...  "<dest>"] # 文件名中有空白字符时使用此种格式

<src>：要复制的源文件或目录，支持使用通配符；
<dest>：目标路径，正在创建的镜像文件的文件系统路径；建议使用绝对路径，否则，则相对于WORKDIR而言；

例如：
COPY  nginx.conf  /etc/nginx/nginx.conf
COPY  *.conf   /etc/nginx/conf.d/

注意：
(0) 所有新复制生成的目录文件的UID和GID均为0；
(1) <src>必须是build命令上下文中的路径，因此，不能使用类似“../some_dir/some_file”类的路径；
(3) <src>如果是目录，递归复制会自动行；如果有多个<src>，包括在<src>上使用了通配符这种情形，此时<dest>必须是目录，而且必须得以/结尾；
(4) <dest>如果事先不存在，它将被自动创建，包括其父目录；
```

### ADD指令
**类似于COPY指令，额外还支持复制TAR文件,tar归档会自动展开到目标路径，以及从URL路径下载**

语法格式：

```
ADD  <src> ...  <dest>
ADD  ["<src>",...  "<dest>"]	

示例：

ADD  haproxy.cfg  /etc/haproxy/haproxy.cfg  
ADD  logstash_*.cnf   /etc/logstash/
ADD  http://flow-files-289777576.mtmssdn0.com/flow-agent-0.9.8-linux   /usr/local/bin/flow-agent

注意：
(0) 以URL格式指定的源文件，下载完成后其目标文件的权限为600；
(1) <src>必须是build上下文中的路径，因此，不能使用类似“../some_dir/some_file”类的路径；
(2) 如果<src>是URL，且<dest>不以/结尾，则<src>指定的文件将被下载并直接被创建为<dest>；如果<dest>以/结尾，则URL指定的文件将被下载至<dest>中，并保留原名；
(3) 如果<src>是一个host本地的文件系统上的tar格式的文件，它将被展开为一个目录，其行为类似于tar  -x命令；但是，如果通过URL下载到的文件是tar格式的，是不会自动进行展开操作的；
(4) <src>如果是目录，递归复制会自动行；如果有多个<src>，包括在<src>上使用了通配符这种情形，此时<dest>必须是目录，而且必须得以/结尾；
(5) <dest>如果事先不存在，它将被自动创建，包括其父目录；
```	

### ENV指令
**定义环境变量，此些变量可被当前dockerfile文件中的其它指令所调用，调用格式为$variable_name或${variable_name}；**

语法格式：

```
ENV  <key>  <value>    一次定义一个变量
ENV  <key>=<value> ...   一次可定义多个变量 ，如果<value>中有空白字符，要使用\字符进行转义或加引号；

例如：
ENV  http_proxy="http://172.31.254.1:8123"   https_proxy="https://172.31.254.1:8123" \
	WORK_DIR="/app"

等同于：
ENV http_proxy http://172.31.254.1:8123
ENV https_proxy https://172.31.254.1:8123
ENV WORK_DIR /app

注意:
(0) 如果有续行符,第二行应该缩进
(1) ENV定义的环境变量在镜像运行的整个过程中一直存在，因此，可以使用inspect命令查看，甚至也可以在docker  (2) run启动此镜像时，使用--env选项来修改指定变量的值；
```	

### USER指令
**指定运行镜像时，或运行Dockerfile文件中的任何RUN/CMD/ENTRYPOINT指令指定的程序时的用户名或UID**

语法格式：

```
USER  <UID>|<Username>

注意：<UID>应该使用/etc/passwd文件存在的用户的UID，否则，docker run可能会出错；
```

### WORKDIR指令
**用于为Dockerfile中所有的RUN/CMD/ENTRYPOINT/COPY/ADD指令指定工作目录**

语法格式：

```
WORKDIR  <dirpath>

例如：
WORKDIR  /app
WORKDIR  $WORK_DIR

注意：WORDIR可出现多次，也可使用相对路径，此时表示相对于前一个WORKDIR指令指定的路径,每一个WORDIR都相当于cd一次；WORKDIR还可以调用由ENV定义的环境变量的值；
```

### VOLUME指令
**用于目标镜像文件中创建一个挂载点目录，用于挂载主机上的卷或其它容器的卷**

语法格式：

```
VOLUME  <mountpoint>
VOLUME  ["<mountpoint>", ...]

注意：如果mountpoint路径下事先有文件存在，docker run命令会在卷挂载完成后将挂载点此前的文件复制到新挂载的卷中；
```

### RUN指令
**用于指定docker build过程中要运行的命令，而不是docker run此dockerfile构建成镜像时运行；每个run都会创建一个新的层,所以建议把多个命令放在一个RUN指令中**

语法格式：

```
RUN  <command> (会自动启动shell进程运行command命令,但由于此shell进程在容器中的PID不唯一,所以不能接受UNIX信号,所以docker stop接受不到任何信号)

RUN  ["<executeable>", "<param1>", "<param2>", ...] 不会专门启动shell进程运行,所以PID唯一,能接受unix信号

RUN ["/bin/bash", "-c", "<executeable>", "<param1>", "<param2>", ...]

例如：
RUN  yum  install  iproute nginx  && yum clean all 
RUN  /tmp/install.sh
```

### CMD指令：
**类似于RUN指令，用于运行程序；但二者运行的时间点不同；CMD在docker run时运行，而非docker build；CMD指令的首要目的在于为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束；不过，CMD指令指定的程序可被docker run命令行参数中指定要运行的程序所覆盖**

语法格式：

```
CMD  <command>  或
CMD  ["<executeable>", "<param1>", "<param2>", ...]  或
CMD [ "<param1>", "<param2>", ...] #第三种为ENTRYPOINT指令指定的程序提供默认参数；

例如:
CMD  ["/usr/sbin/httpd", "-c","/etc/httpd/conf/httpd.conf"]

注意：如果dockerfile中存在多个CMD指令，仅最后一个生效；
```

### ENTRYPOINT指令：
**类似于CMD指令，但其不会被docker run的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给ENTRYPOINT指令指定的程序；但是，如果运行docker  run时使用了--entrypoint选项，此选项的参数可当作要运行的程序覆盖ENTRYPOINT指令指定的程序**

语法格式：

```
ENTRYPOINT  <command> 或
ENTRYPOINT   ["<executeable>", "<param1>", "<param2>", ...]	

例如：(相当于 top -b -c )
CMD ["-c"]
ENTRYPOINT  ["top", "-b"]
```

### EXPOSE指令
*用于为容器指定要暴露的端口*

语法格式：

```
EXPOSE   <port>[/<protocol>]  [<port>[/<protocol>]] ... # <protocol>为tcp或udp二者之一，默认为tcp；

例如：
EXPOSE  11211/tcp  11211/udp 
```


## docker build:从dockerfile构建image

```
Usage:	docker build [OPTIONS] PATH | URL | -  # PATH 为此次build的工作路径

常用选项:
	-f, --file=                     指定dockerfile(Default is 'PATH/Dockerfile')
	--force-rm=false                #无论构建成功与否,都删除中间容器
	-m, --memory=                   #内存限制,指定最大内存
	--memory-swap=                  #指定最大内存(memory + swap),'-1' 表示禁用swap
	--no-cache=false                #构建时不使用本地缓存的镜像
	--rm=true                       #构建成功后删除中间产生的容器
	--shm-size=                     #设置shm(share memery,/dev/shm)的大小,默认为 64MB
	-t, --tag=                      #指定image的名称和tag
```

``` dockerfile
FROM busybox:latest
MAINTAINER Mayi <my@fir.im>

COPY index.html  /web/html/index.html

EXPOSE 80/tcp

CMD ["httpd","-f","-h","/web/html"]
```

```
~]# docker build  -f  ./httpd.df  -t  busybox:web   . # 构建
~]# docker images # 查看
~]# docker run  -P  --name httpd  busybox:web #运行
~]# docker port httpd # 查看端口
```





