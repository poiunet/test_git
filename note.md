### docker基础命令
systemctl start docker 启动docker

docker version 查看版本  
docker info 显示当前docker状态  
docker 显示可以的docker命令  
docker container ps  列出当前正在运行的容器   
docker container ps -a 列出当前所有的容器   
docker system prune -f 清理删除已经退出的容器   
docker image prune -a   清理没有使用的镜像
docker network ls   查看docker 提供的网络模式
   

### image镜像
镜像：包含文件系统，源码，库文件，依赖工具等运行application所需要的文件  
container容器：一个运行中的docker image   

#### 镜像命令
docker image ls 列出当前系统上存在的镜像 
#### 容器命令
docker container ls  列出当前正在运行的容器   
docker container run nginx  运行nginx    
docker container stop nginx 停止nginx 可以只写开头的字母   
docker container ls 可以省略为docker ls     
docker container stop 加多个字符 可以停掉多个容器   
docker container ps -aq 列出所有container的id    
docker container stop $(docker container ps -qa) 批量关闭container  
docker container rm $(docker container ps -qa) 批量删除container   
不能删除正在运行的container，可以强制删除 docker container rm XXXXXX -f   
docker container run --rm -it 加参数--rm 运行完就删除  
attached模式 前台模式 前台执行  
detached模式 后台模式 docker container run -d    
docker attach XXID 可以变回attached模式  
一般不使用attached模式(交互模式)
docker container run -d -p 80:80 nginx 创建端口映射的nginx  
docker container logs XXXID 打印日志  
docker container stop XXXXID 停止容器  
docker container pause  XXXXID  暂停容器
docker container unpause  XXXXID  暂停容器
docker container logs -f XXXID 动态跟踪日志  
docker container run -it ubuntu sh 交互模式启动 退出就关闭了
docker exec -it XXXid sh 进入交互模式 退出不关闭 在一个已经运行的容器里执行一个额外的命令
docker container exec -it XXXID sh  
docker container top 容器ID 查看容器中进程情况

docker container run -m 200M --memory-swap=400M nginx 指定占用的磁盘交换空间做内存 
#### 镜像获取 
1. 镜像获取的3种方式
2. 从registry拉取（公私）  
3. dockerfile构建 需联网  
4. 导入（离线）  

镜像的拉取  docker image pull nginx  
docker image ls 列出本地存在的镜像  
docker image pull nginx：1.20.0  拉取特定版本  
docker image inspect imageID  查看镜像详细  
docker image history imageXXXid   查看镜像分层

镜像导入导出  
导出docker image save nginx:1.20.0 -o 文件名   
导入docker image load -i 文件名  

##### 构建dockerfile 
事先创建好Dockerfile（默认的文件名就叫Dockerfile）  
docker image build -t hello .  
docker image build -t 镜像名称 Dockerfile所在目录  
docker image build -t 镜像名称 -f dockerfile文件   所在目录  

修改镜像标签  
方法一. docker image build -t poiunet/hello:1.0 .   
方法二. docker image tag hello poiunet/hello:1.0

 删除镜像 docker image rm poiunet/hello:1.0
 
 ##### 推送镜像
 登录dockerhub docker login  
 ###### 推送image  
 docker image push poiunet/hello:1.0
 ###### 拉取image  
 docker pull poiunet/hello:1.0  (版本号要指定，除非指定了lastest版本)
 
 
 
#### 从容器创建镜像

docker container commit 容器id 镜像名称  
docker container commit e1e0   poiunet/nginx-hello



#### 基础镜像选择原则
1. 尽量选官方的其次是开源的
2. 固定版本tag 不要用latest
3. 选体积小的  
   
#### DockerFile文件编辑指令  
dockerfile文件中执行run最好写在一行，可以减少分层从而减少空间。  
###### 文件复制命令 copy和add  
都可以在复制时创建不存在的目录  
add在复制文件时可以解压缩文件

###### workdir 用于目录切换

###### arg和env
可以用来设置变量 ，env设置变量可以设置构建过程和容器内部的变量  
arg只能用在构建过程中

###### cmd和entrypoint
CMD可以用来设置容器启动时默认执行的命令  
如果docker container run启动容器时制定了其他命令，则CMD命令会被忽略。  
如果定义了多个cmd，只有最后一个会被执行  

entrypoint 可以设置容器启动时要执行的命令  
cmd所设置的命令，可以在docker container run 时传入其他命令覆盖掉cmd命令，但是entrypoint所设置的命令一定会被执行    
cmd和entrypoint同时支持shell和exec格式  
shell格式 cmd echo "hello docker"   entrypoint echo "hello docker"   
exec格式 entrypoint ["echo","hello docker"]  cmd ["echo","hello docker"]   

例：docker container run --rm -it demo-ent  echo "hello world"  
会打印hello dicker hello world 后加的命令会被当成字符串， 因为entrypoint定义的命令必须被执行  


###### 缓存cache
当某一层发生变化后该层以及该层之后的层不能再使用cache，所以可以把经常改变的内容尽量放到后边。

###### dockerignore 
设置docker忽略的文件，可以提构建速度，减少发送到后台构建的文件数


##### 多阶段构建
可以as一个有设定环境的镜像，然后从中拷贝出编译好或者需要的文件


##### 构建的注意事项
容器中尽量使用非root用户 （可以自己创建用户）  
github上docker-library/official-images中有很多官方dockerfile

##### docker的数据持久化
在运行中的容器里创建的文件，都被保存在一个可写的容器层  
docker主要提供两种方式做数据持久化 data volume和bind mount
###### data volume
查看 docker volume ls
查看具体docker volume inspect XXXXXX

```FROM alpine:latest
RUN apk update
RUN apk --no-cache add curl
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.1.12/supercronic-linux-amd64 \
    SUPERCRONIC=supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=048b95b48b708983effb2e5c935a1ef8483d9e3e
RUN curl -fsSLO "$SUPERCRONIC_URL" \
    && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
    && chmod +x "$SUPERCRONIC" \
    && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
    && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic
COPY my-cron /export/dockercode/my-cron
WORKDIR /export/dockercode

# RUN cron job
CMD ["/usr/local/bin/supercronic", "/export/dockercode/my-cron"]
```
my-cron 
```
*/1 * * * * date >> /export/dockercode/test.txt```

构件时用-v参数指定要持久化的目录和持久化到的目录
```
$ docker image build -t my-cron .
$ docker container run -d -v cron-data:/export/dockercode my-cron
43c6d0357b0893861092a752c61ab01bdfa62ea766d01d2fcb8b3ecb6c88b3de
$ docker volume ls
DRIVER    VOLUME NAME
local     cron-data
$ docker volume inspect cron-data
[
    {
        "CreatedAt": "2021-06-22T23:25:02+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/cron-data/_data",
        "Name": "cron-data",
        "Options": null,
        "Scope": "local"
    }
]
$ ls /var/lib/docker/volumes/cron-data/_data
my-cron
$ ls /var/lib/docker/volumes/cron-data/_data
my-cron  test.txt
```

###### 环境清理
强制删除所有容器，系统清理和volume清理

```
$ docker rm -f $(docker container ps -aq)
$ docker system prune -f
$ docker volume prune -f
```


### docker网络
创建执行命令的容器

```
 docker container run -d --rm --name box1 busybox /bin/sh -c "while true; do sleep 3600; done"
```

 docker network ls  可以看到docker提供的网络模式

 docker network inspect 加网络模式的Id
 可以看到网络模式的详细信息

建立网络驱动 docker network create -d bridge mybridge

容器指定网络驱动 --network XXX驱动名
 docker container run -d --rm --name box2 --network mybridge busybox /bin/sh -c "while true; do sleep 3600; done"

 容器增加网络
 docker network connect bridge box3
 容器关闭网络
 docker network disconnect bridge box3

 

```
 docker container run -d --rm --name box1 busybox /bin/sh -c "while true; do sleep 3600; done"
```

 docker network ls  可以看到docker提供的网络模式

 docker network inspect 加网络模式的Id
 可以看到网络模式的详细信息
 
 创建网络
 docker network create -d bridge mybridge
 
 默认的docker0网络不提供名字互ping
 
 指定网段
 docker network create -d bridge --gateway 172.200.0.1 --subnet 172.200.0.0/16 XXX名字
 
 查看容器配置中的IP地址
 docker container inspect --format '{{.NetworkSettings.IPAddress}}' XXX

```

WARNING: IPv4 forwarding is disabled. Networking will not work.解决方法

```
修改文件
vim /etc/sysctl.conf
添加或修改
#配置转发
net.ipv4.ip_forward=1
修改完
#重启服务，让配置生效
systemctl restart network
#查看是否成功,如果返回为“net.ipv4.ip_forward = 1”则表示成功
sysctl net.ipv4.ip_forward
```

linux命令查看iptables配置

```iptables -t nat -nvxL
iptables -t nat -nvxL
```

dockerfile 中EXPOSE只是起到说明的作用 ，并不影响使用-p来映射端口



host类型网络创建容器时可以用--network host 指定

host类型可以使容器和主机共享同一网络，容器的端口就是本机的端口，这类容器只能启动一个

none类型网络表示没有网络，docker不创建网络，需要用户再创建


docker compose 安装
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
如果访问失败用
```
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.4.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

将可执行权限应用于二进制文件：
```
sudo chmod +x /usr/local/bin/docker-compose
```

测试是否安装成功：
```
docker-compose version
Docker Compose version v2.4.1
```

编写好docker-compose.yml 然后启动 docker-compose up
docker-compose创建的网络和容器都会以当前文件夹名为开头后面再加命名的名字
后台运行 docker-compose up -d，后台运行时如果想看log可以通过 docker-compose logs 查看，docker-compose logs -f 持续查看
docker-compose ps 查看列表 （必须在compose 目录运行）
可以用-f参数指定compose文件名
停止 docker-compose stop
删除 docker-compose rm
启动时加参数-p可以指定前缀名，如：docker-compose -p XXXX up -d （但是以后使用docker-compose 都需要加上参数-p XXXX  否则显示不了容器）

docker-compose.yml中可以加入build 构建需要的镜像， 同时可以用image指定标签
```
build:
  context:目录
  dockerfile:dockerfile文件名
```

查看compose的容器状态 docker-compose ps

如果Dockerfile 发生改变怎么使 docker-compose更新
1. docker-compose bulid 全部build下的重新构建， docker-compose bulid  XXXX 重新构建指定的容器
2.在up时加参数 --build 如  docker-compose up -d --build 可以在up时执行

如果yml文件中删除了某个容器，则要使用--remove-orphans参数，如  docker-compose up -d --remove-orphans

重启构建的容器 docker-compose restart

即使yml文件中不指定网络，compose也会创建网络,创建的容器属于同一网络，可以ping name和SERVICE都可以ping通
docker-compose会内置一个dns 容器之间访问会先访问这个dns 

docker-compose rm 删除（需要先停止容器）
容器镜像被彻底清理时 创建容器时创建的网络也会被清理

docker-compose 水平扩展 docker-compose up -d  --yml文件所在文件夹名 sevice名=扩展数量  如:docker-compose up -d --scale flask=3（数量可以增加或者减少）
水平扩展就是docker帮忙做了负载均衡，如在ping sevice名时会返回不同的ip

指定环境变量 docker-compose --env-file .\环境变量文件 up -d