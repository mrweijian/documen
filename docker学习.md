**Dockerfile**

```
FROM java:8
MAINTAINER weijian
ADD /home/mall-web-0.0.1-SNAPSHOT.jar /mall-web-0.0.1-SNAPSHOT.jar
EXPOSE 8089
ENTRYPOINT ["java","-jar","mall-web-0.0.1-SNAPSHOT.jar"]

```

**docker构建镜像**

```
`docker build -t mall-web:1.0 .
# -f指定Dockerfile文件的路径

# -t指定镜像名字和TAG

# .指当前目录，这里实际上需要一个上下文路径
```

**docker 常用命令**

```
docker inspect 容器ID或容器名                #查看容器元信息
docker cp 主机文件路径 容器ID或容器名:容器路径  #主机中文件拷贝到容器中
docker cp 容器ID或容器名:容器路径 主机文件路径  #容器中文件拷贝到主机中
docker exec -it 容器ID或者容器名 /bin/bash   #进入正在运行容器
docker rm -f 容器ID或容器名                 # -f 表示强制删除
docker logs 容器ID或容器名                  #查看日志
docker start 容器ID或容器名                 #启动容器
docker stop 容器ID或容器名                  #停止容器

docker ps -a -q                
# docker ps查看正在运行的容器 -a 查看所有容器（运行中、未运行）
# -q 只查看容器的ID

docker run --name 容器名 -i -t -p 主机端口:容器端口 -d -v 主机目录:容器目录:ro 镜像ID或镜像名:TAG
# --name 指定容器名，可自定义，不指定自动命名
# -i 以交互模式运行容器
# -t 分配一个伪终端，即命令行，通常-it组合来使用
# -p 指定映射端口，讲主机端口映射到容器内的端口
# -d 后台运行容器
# -v 指定挂载主机目录到容器目录，默认为rw读写模式，ro表示只读

docker rmi -f 镜像ID或者镜像名:TAG
# 删除指定本地镜像
# -f 表示强制删除

docker commit -m="描述消息" -a="作者" 容器ID或容器名 镜像名:TAG
# 例:
# docker run --name mytomcat -p 80:8080 -d tomcat

#docker exec -it mytomcat /bin/bash
#cd webapps/ROOT
#rm -f index.jsp
#echo hello world > index.html
#exit

# docker commit -m="修改了首页" -a="魏小建" mytomcat huaan/tomcat:v1.0
```

**docker目录**

```
/var/lib/docker/ docker存储位置
/var/lib/docker/image/overlay2 存储镜像管理数据的目录，以使用的存储驱动命名
/var/lib/docker/image/overlay2/distribution  从远端拉到本地的镜像相关元数据
/var/lib/docker/image/overlay2/imagedb 镜像数据库
/var/lib/docker/image/overlay2/imagedb/content 每镜像层次的配置digest,也就是镜像ID。
/var/lib/docker/image/overlay2/layerdb  镜像每个layer的元数据

```

**docker网络配置**

```
# 配置容器的主机名
docker run --name t1 --network bridge -h [自定义主机名] -it --rm busybox
# 自定义DNS
docker run --name t1 --network bridge --dns 114.114 -it --rm busybox
# 给host文件添加一条
docker run --name t1 --network bridge --add-host [hostname]:[ip] -it --rm busybox
```

**docker-compose**

```
version: '1'
services:
  eureka:
    image: mall-eureka:1.0
    ports:
      - 3000:3000
    expose:
      - 3000
  web:
    image: mall-eureka:1.0
    ports:
      - 8089:8089
    expose:
      - 8089
```

```
docker-compose up -d
```



