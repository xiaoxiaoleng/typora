##### 进入docker容器

```shell
docker exec -ti  <your-container-name>   sh  | docker exec -it 0606ec23972a sh

docker exec -ti  <your-container-name>   /bin/sh
```

##### 查看容器服务

```
docker ps
docker ps|grep mongo
```

##### 将容器的ip192.168.0.100和80端口，映射到宿主机的8000端口

```
docker run -p 192.168.0.100:8000:80 -it ubuntu /bin/bash
#将nginx的80端口映射到宿主机的800端口上
docker run -d -it -p 800:80 nginx 
```

##### 查看映射端口配置

```
docker port container_ID #容器ID
#结果输出
80/tcp -> 0.0.0.0:800
```

##### 查看镜像哈希值

```
docker images --digests
```

